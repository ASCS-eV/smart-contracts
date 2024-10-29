# EVES-003: TZIP-21 rich metadata specification

## Asset definition

An asset is defined by the [GaiaX 4 PLC-AAD Manifest Ontology](https://github.com/GAIA-X4PLC-AAD/ontology-management-base/tree/main/manifest/). The example in this folder is taken from Release v0.1.4 from the [hd-map-asset-example](https://github.com/GAIA-X4PLC-AAD/hd-map-asset-example).

## Privacy layer

| manifest:accessRole  | ENVITED-X domain                                          | Comment                               |
| -------------------- | --------------------------------------------------------- | ------------------------------------- |
| "owner"              | https://assets.envited-x.net/Asset-CID                    | CID v1, signed URLs, Asset credential |
| "registeredUser"     | https://metadata.envited-x.net/Asset-CID                  | CID v1, signed URLs, DEMIM credential |
| "publicUser"         | ipfs://Asset-CID -> https://ipfs.envited-x.net/Asset-CID  | CID v1, public, indexer to new URL    |

## Asset validation

1) Open asset.zip
2) Validate manifest.json
3) Locate all content referenced in manifest.json (file name is static)
4) Locate domainMetadata.json (name according to manifest.json)
5) Validate domainMetadata.json
6) If all green, offer upload 
7) Calculate CID of asset.zip -> rename and store in bucket: https://assets.envited-x.net/Asset-CID
8) Create copy of CID_manifest.json for replacing relative paths with URLs
9) Upload "publicUser" information to IPFS -> links in CID_manifest.json
10) Store "registeredUser" information in bucket: https://metadata.envited-x.net/Asset-CID -> links in CID_manifest.json
11) Create CID_tzip21_token_metadata.json -> fill information according to mapping table
12) Mint token
13) Catch event with indexer -> store information in permanent DB
14) Upload "publicUser" information from IPFS to bucket: https://ipfs.envited-x.net/Asset-CID

Note: The CID is the connecting identifier between all systems. If additional non-public information need to be stored in the permanent DB that can only be known before the mint then this relation may help to associate information.

## TZIP-21 token metadata mapping

Attributes not in the table are static and the same for every mint. Examples are the first five tags or "publishers", which is always ENVITED-X and the ASCS as the mint is conducted through the website.

| TZIP-21       | EVES-003      | Comment       |
| ------------- | ------------- | ------------- |
| "name" | hdmap:general:name |  |
| "description" | hdmap:general:description |  |
| "tags" | hdmap:format:formatType + " " + hdmap:format:version | All tags static except for the format |
| "minter" | Member associated with user | This must be the same as the view from the revocation registry contract returns |
| "creators" | Name of the company | Taken from the company profile the user belongs to |
| "date" | - | System [date-time](https://json-schema.org/understanding-json-schema/reference/string#dates-and-times)  |
| "rights" | manifest:licenseType | Open-source license SPDX identifier OR "All rights reserved" |
| "rightsUri" | Link to full os license text OR Smart contract for individual license | The policy smart contract will follow later on |
| "artifactUri" | https://assets.envited-x.net/Asset-CID |  |
| "identifier" | Asset-CID | Cell 1, Row 2 |
| "externalUri" | uploaded domainMetadata.json to IPFS |  |
| "displayUri" | manifest:contentData:visualization | Always use the first media image |
| "formats" | Add info for artifactUri, externalUri and displayUri | |
| "attributes" | Same as in example with IPFS CIDs+URL |  |