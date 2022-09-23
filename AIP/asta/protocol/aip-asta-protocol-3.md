---
aip: aip-asta-protocol-3
title: Adding multi-chain provider
status: Draft
type: Standard
author: Piyush Jaiswal <iampiyushjaiswal103@gmail.com>
created: 2022-09-21
updated: 2022-09-2
---

## Title

Adding multi-chain provider node

## Abstract
**Protocol** provides easy option for developer to config their custom rpc nodes. 

## Specification

Schema of data stored for the node.
```ts
interface RPCNodeData{
    url: string;
    chainIds: string[];
    nodeId: string;
    created_at: Date;
    project: Project;
    meta: Object;       
}
```
* `url` will be an encrypted property storing the rpc url.
* `chaiIds` containing all the chainIds supported by the node.
* `nodeId` is the primary  key for  the node which will be used to reference the node.
* `meta` contains all the meta data associated with the node such as chain names, img, etc.
* `project` is the `ForeignKey` to the project  it was associated with.


### Access to methods
Since each method executed in the **Protocol** requires a node to connect, either it will use public nodes available or dedicated nodes.

*  Node to connect can be directly provided as a query params with key `nodeId` in each request. 
* If no node is provided and then method will fallback to the public nodes if available otherwise it will throw an error.

### Methods

- **Create RPC Node**
    * POST method creating rpc node with selection properties
    * Requires `service  API Key` for creation of the node.
    * Parameters:
        -  url
        - chainIds
- **Get RPC Node**:
    -  GET  request to fetch details
    - Client API Key is required
    - Parameter as query params:
        - nodeId
    - Response:
        -   nodeId
        -  chainIds
        -   meta
- **Update chainIds or meta**:
    - PUT request
    - service API Key required
    - Parameters:
        - nodeId
        - chainIds?
        - meta?

- **Remove node**:
    - DELETE request
    - service API KEY is required
    - Parameters:
        - nodeId
    
