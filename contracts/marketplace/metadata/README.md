# EVES-003: ENVITED Asset definition

An asset is defined by the [GaiaX 4 PLC-AAD Manifest ontology](https://github.com/GAIA-X4PLC-AAD/ontology-management-base/tree/main/manifest/). The example in this folder is taken from Release v0.1.6 from the [hd-map-asset-example](https://github.com/GAIA-X4PLC-AAD/hd-map-asset-example).

## Pinata IPFS

Public information is provided via [IPFS](https://ipfs.tech/). A commercial pinning service is [Pinata](https://pinata.cloud/), which is used and recommended here.

### CID v1
By default, uploads through the Pinata web interface return a v0 CID which are prefixed with *Qm*. In ENVITED it is recommended to use [CID v1](https://docs.ipfs.tech/concepts/content-addressing/#version-1-v1). The only way you can change this is via the API, not in the web app. You can change them with *pinataOptions*, as outlined [here](https://docs.pinata.cloud/web3/pinning/pinata-metadata#pinataoptions).

### Double file extension
When uploading a file you have to rename your file e.g. *file* or *image* instead of *file.json* or *image.png* excluding the data extension suffix, because Pinata saves it exactly as is. When you go to download the file, the system will keep that original file name, but since your local system recognizes the file type, it adds the extension again automatically. So, *file.json* would download as *file.json.json* because it's applying the *.json* extension to the full file name *file.json*.

## Privacy layer

| manifest:accessRole  | ENVITED-X domain                                                  | Comment                               |
| -------------------- | ----------------------------------------------------------------- | ------------------------------------- |
| "owner"              | https://assets.envited-x.net/Asset-CID                            | CID v1, signed URLs, Asset credential |
| "registeredUser"     | https://metadata.envited-x.net/Asset-CID                          | CID v1, signed URLs, DEMIM credential |
| "publicUser"         | ipfs://Data-CID -> https://ipfs.envited-x.net/Asset-CID/Data-CID  | CID v1, public, indexer to new URL    |

## Asset validation

1) Open asset.zip
2) Validate manifest.json
3) Locate all content referenced in manifest.json (file name is static)
4) Locate domainMetadata.json (name according to manifest.json)
5) Validate domainMetadata.json
6) If all green, offer upload
7) Calculate CID of asset.zip -> rename and store in bucket: https://assets.envited-x.net/Asset-CID
8) Create copy of manifest.json named tzip21_asset_manifest.json for replacing relative paths with URLs
9) Upload "publicUser" information to IPFS -> links in tzip21_asset_manifest.json
10) Store "registeredUser" information in bucket: https://metadata.envited-x.net/Asset-CID -> links in tzip21_asset_manifest.json
11) Create tzip21_token_metadata.json -> fill information according to mapping table
12) Mint token
13) Catch event with indexer -> store information in permanent DB
14) Download "publicUser" information from IPFS to bucket: https://ipfs.envited-x.net/Asset-CID
Download

Note: The CID is the connecting identifier between all systems. If additional non-public information needs to be stored in the permanent DB that can only be known before the mint then the CID may help to associate information.

## TZIP-21 rich metadata mapping

Attributes not in the table are static and the same for every mint. Examples are the first five tags or "publishers", which is always ENVITED-X and the ASCS as the mint is conducted through the website.

| TZIP-21            | EVES-003                                             | Comment                                                      |
| -------------------| ---------------------------------------------------- | ------------------------------------------------------------ |
| "name"             | hdmap:general:name                                   |                                                              |
| "description"      | hdmap:general:description                            |                                                              |
| "tags"             | hdmap:format:formatType + " " + hdmap:format:version | All tags static except for the format                        |
| "minter"           | Member DID associated with user initiating the mint  | Returned by the View from the DEMIM revocation registry      |
| "creators"         | Name of the company                                  | Taken from the company profile the user belongs to           |
| "date"             | [System date-time][1]                                |                                                              |
| "rights"           | "manifest:spdxIdentifier"                            | [SPDX identifier][2]                                         |
| "rightsUri"        | "manifest:licenseData:manifest:path"                 | Full os license text URL OR policy smart contract did        |
| "artifactUri"      | https://assets.envited-x.net/Asset-CID               |                                                              |
| "identifier"       | Asset-CID                                            |                                                              |
| "externalUri"      | Uploaded domainMetadata.json to IPFS                 |                                                              |
| "displayUri"       | "manifest:contentData:visualization"                 | Always use the first media image                             |
| "formats"          | Add info for artifactUri, externalUri and displayUri |                                                              |
| "attributes"       | Same as in example with IPFS CIDs+URL                | For other asset types hdmap would be exchanged               |

### Custom SPDX license identifier

* Custom license in a LICENSE file in the asset.zip root folder: "LicenseRef-Custom-Commercial-Agreement"
* Custom license in a smart contract as json-ld ODRL policy: "LicenseRef-Policy-Smart-Contract"

[1]: https://json-schema.org/understanding-json-schema/reference/string#dates-and-times
[2]: https://softwareengineering.stackexchange.com/a/450839/443441
