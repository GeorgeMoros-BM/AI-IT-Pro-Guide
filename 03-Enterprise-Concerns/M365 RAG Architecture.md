# M365 RAG Architecture

Microsoft 365 Copilot doesn't deploy into your Azure subscription. Microsoft runs Copilot and its retrieval as a service. Azure holds what you add: a custom agent and a knowledge layer for content Copilot can't retrieve the way you need.

Checked against Microsoft documentation on November 5, 2026. Preview status changes often, so confirm it before you commit to a design.

Scroll sideways to see the full diagram.
![[Pasted image 20261105213525.png]]
- Copilot path, steps 1 to 4
- Custom agent path, steps A to E
- Indexing, runs ahead of any question
- Copy path to avoid

Both paths answer from your content under one Entra identity. Only two request edges cross the boundary between Microsoft's service and your subscription, and each one needs a security decision.

## Recommended build order

This is my recommendation, not a Microsoft requirement. Each step adds operating work, so move on only when the previous one can't meet the need.

1. Fix SharePoint permissions and retire stale content, because Copilot answers from what each user can already open.
2. Use Copilot and declarative agents for Microsoft 365 content.
3. Add Copilot connectors for outside content.
4. Add Azure when you need custom chunking, ranking, or metadata filters, your own orchestration or models, or channels outside Microsoft 365.

## How a question moves

### Copilot path

1. The employee sends a prompt from a Copilot surface.
2. Copilot queries Microsoft Graph and the semantic index as that user. Results are limited to what the user can open.
3. Copilot sends the prompt and grounding to a foundation LLM.
4. Copilot returns the answer to the surface.

### Custom agent path

1. The employee opens your agent in Copilot. The message travels through Azure Bot Service to the Foundry agent.
2. The agent calls the knowledge base and must pass the user's token.
3. The knowledge base queries Azure AI Search and, if configured, SharePoint through the Retrieval API.
4. The agent sends the extracts and the prompt to a Foundry model.
5. The answer returns through Azure Bot Service to the Copilot surface.

## What each piece is responsible for

| Piece | Responsible for | Watch for |
| --- | --- | --- |
| Microsoft 365 tenant, run by Microsoft |  |  |
| Copilot surfaces | Take the prompt and show the answer in Copilot Chat, Teams, Word, Excel, and Outlook. | Copilot Chat without the add-on can't use organizational data through Microsoft Graph unless a pay-as-you-go agent provides it. |
| Copilot orchestrator | Decides how to answer, gets grounding as the user, and calls the model and any enabled agents. | You can't change its logic. Admins choose which agents are allowed. |
| Agents built in Microsoft 365 | Declarative agents scope Copilot to set instructions, knowledge, and actions. Copilot Studio agents add low-code orchestration and can use Azure AI Search as a knowledge source. | Declarative agents inherit Microsoft 365 compliance. Shared tenant data is metered in Copilot Credits for users without the add-on. Copilot Studio's Azure AI Search page doesn't say whether results are trimmed per user. |
| Foundation LLMs | Generate the response. A real-time router selects the model by default. | Calls can route to other regions during high use. Prompts and responses aren't used to train foundation LLMs. |
| Microsoft Graph and semantic index | Connect Copilot to organizational content, and find it by keyword and meaning within the user's access. | New documents on accessible sites are indexed daily, and edits to indexed documents immediately. Microsoft documents no chunking or ranking tuning. |
| Microsoft 365 content | Stays the system of record for files, mail, chats, and their permissions. | Copilot can surface anything a user can view, so oversharing becomes a Copilot problem. |
| Copilot connectors | Synced connectors index outside content and its ACLs into Microsoft Graph. Federated connectors fetch it live through MCP. | Federated content stays in the source system. Custom connectors use the synced tenant configuration. |
| Your Azure subscription, run by you |  |  |
| Azure Bot Service | Carries activity protocol messages between Microsoft 365 and your agent. Foundry creates it when you publish. | Microsoft 365 requires a publicly routable agent endpoint. Organization-wide publishing needs Microsoft 365 admin approval. |
| Foundry Agent Service | Runs your custom engine agent: orchestration, tools, conversation state, agent identity, and tracing. | You own compliance, responsible AI, and security for this agent. |
| Foundry IQ knowledge base | Gives agents one retrieval endpoint. Queries its sources, merges results, and returns extracts with references. | Stable API `2026-04-01` covers search index, Blob, OneLake, and web sources. LLM query planning, answer synthesis, and remote SharePoint are Preview |
| Azure AI Search | Stores chunks and vectors, runs hybrid search and semantic ranking, and enforces document-level access. | Security filters are generally available, but they are a string match your app supplies. Token-based ACL and Purview label enforcement are Preview |
| Indexer and skillset | Pull changes from sources, chunk and embed text, and sync metadata and permissions. | Source permission changes reach results only after a re-sync. Your Retrieval Corpus Lifecycle rule applies here. |
| Blob, ADLS, or OneLake | Hold outside content chosen for indexing. | This is a copy, so it needs its own access and retention design. |
| Foundry Models | Generate answers and create embeddings. | Chunks and questions must use the same embedding model. Changing it means rebuilding the index. |
| Outside both zones |  |  |
| Line-of-business systems | Stay the system of record for data outside Microsoft 365. | Questions about current records may suit an API call better than RAG. |
| Control plane |  |  |
| Microsoft Entra ID | Identifies users and agents, and supplies the user identity behind query-time trimming. | Remote SharePoint needs Microsoft 365 and Azure under one Entra tenant. OAuth passthrough needs the user in the project's tenant. |
| Microsoft Purview | Applies sensitivity labels, DLP, audit, retention, and eDiscovery to Copilot content and interactions. | Agents inherit their parent app's controls. Confirm coverage for each Foundry app. |
| Microsoft Agent 365 | Inventories agents, controls which data and tools each can reach, and logs agent actions. | Licensed per user, either standalone or through Microsoft 365 E7. |
| Foundry Control Plane | Monitors Foundry agents: evaluations, guardrails, compliance posture, and cost. | Available through the Foundry portal only. |

## Where each source should go

This routing is my recommendation. It keeps one index and one permission model for each source wherever possible.

| Source | Route | Why |
| --- | --- | --- |
| SharePoint, OneDrive, mail, Teams | Leave in place. Copilot reaches it through Microsoft Graph. | Avoids a second index and a copy of the permissions. |
| SharePoint for a custom agent | Remote SharePoint knowledge source or the Work IQ tool Preview | Queries live as the user, so no index or ACL sync is needed. |
| Outside content for Copilot users | Synced Copilot connector. | Microsoft runs the index and the permission filtering. |
| Live or sensitive outside data | Federated Copilot connector. | Data stays in the source system. |
| Outside content that needs custom retrieval | Azure AI Search through the indexer. | You get control of chunking, ranking, and filters, and you take on the operating work. |
| Current records and transactions | API or MCP action. | RAG retrieves passages. Live records need a direct query. |

**Your evidence policy works differently on each path.** On the Azure path, metadata filters can exclude unapproved or superseded chunks at query time. On the Copilot path, you don't control that filter step, so old versions have to leave the sites and libraries in scope. That is my reading of the documentation, so confirm it in your pilot.

## Verify before you build

| Risk | What Microsoft documents | What to do |
| --- | --- | --- |
| Permission trimming can fail open | "Without the token, permission-enabled sources return results unfiltered." I found no page confirming that the user's token reaches the knowledge base when Copilot calls a published agent. | Before any pilot, test with two users who have different access. Scope the project's managed identity narrowly. |
| Public agent endpoint | Microsoft 365 doesn't support private network connectivity for agents. Source IP filtering is defense in depth only. | Bring security into the design now, because this can block a private-only standard. |
| Citations may not reach the user | The network-isolated publishing article lists streaming and citations as not supported for published agents. | Test it. If it holds, have the agent write the source and section into the answer text. |
| Filters can fail silently | Invalid KQL in a Retrieval API filter runs without scoping and returns no error. | Add scoped queries to your release tests. |
| SharePoint indexer limits | It's in preview. It doesn't support tenants with Entra Conditional Access or private endpoints. | Don't plan on it for production. Microsoft points to remote SharePoint, or Copilot Studio first for production chat. |
| Retrieval limits and licensing | 200 requests per user per hour and 25 results per query. Each remote SharePoint user needs the Copilot add-on or pay-as-you-go Preview | Size the pilot against these limits and confirm who will be licensed. |

## Sources

- [How Microsoft Copilot works](https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-architecture)
- [Data, privacy, and security for Copilot](https://learn.microsoft.com/en-us/microsoft-365/copilot/microsoft-365-copilot-privacy)
- [Semantic indexing for Microsoft 365 Copilot](https://learn.microsoft.com/en-us/microsoftsearch/semantic-index-for-copilot)
- [Compare declarative and custom engine agents](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/agents-overview)
- [Licensing and cost for Copilot extensibility](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/cost-considerations)
- [Copilot connectors overview](https://learn.microsoft.com/en-us/microsoft-365/copilot/connectors/overview)
- [Microsoft 365 Copilot Retrieval API](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/api/ai-services/retrieval/overview)
- [Azure AI Search in Copilot Studio](https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-azure-ai-search)
- [Foundry Agent Service](https://learn.microsoft.com/en-us/azure/foundry/agents/overview)
- [Publish Foundry agents to Copilot and Teams](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/publish-copilot)
- [Publish network-isolated agents](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/publish-copilot-virtual-network)
- [Connect agents to Foundry IQ knowledge bases](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/foundry-iq-connect)
- [MCP authentication in Foundry](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/mcp-authentication)
- [Work IQ in Foundry](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/work-iq)
- [What is Foundry IQ?](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq)
- [Agentic retrieval versions](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-how-to-migrate)
- [Remote SharePoint knowledge source](https://learn.microsoft.com/en-us/azure/search/agentic-knowledge-source-how-to-sharepoint-remote)
- [RAG in Azure AI Search](https://learn.microsoft.com/en-us/azure/search/retrieval-augmented-generation-overview)
- [Document-level access in Azure AI Search](https://learn.microsoft.com/en-us/azure/search/search-document-level-access-overview)
- [SharePoint indexer](https://learn.microsoft.com/en-us/azure/search/search-how-to-index-sharepoint-online)
- [Purview for Microsoft 365 Copilot](https://learn.microsoft.com/en-us/purview/ai-m365-copilot)
- [Purview for AI apps](https://learn.microsoft.com/en-us/purview/ai-microsoft-purview)
- [Microsoft Agent 365](https://www.microsoft.com/en-us/microsoft-agent-365)
- [Foundry Control Plane](https://learn.microsoft.com/en-us/azure/foundry/control-plane/overview)