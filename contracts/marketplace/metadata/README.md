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

## Asset validation/creation steps

### (1) Client side pre-validation

* Drag and drop asset.zip into upload field
* Open asset.zip on local computer
* Validate manifest.json
  1) Check json correctness with manifest shacle
  2) Locate all content referenced in manifest.json (file name is static)
  3) Differentiate between local and remote files (local = relative path in asset.zip, remote = https:// URL)
  3) Locate domainMetadata.json (name according to manifest.json)
* Validate domainMetadata.json
  1) Read necessary shacles from domainMetadata.json context
  2) Check json correctness with domain shacles

### (2) Upload asset to ENVITED-X Data Space
* On click "upload button"
* Calculate CID of asset.zip
* Rename asset.zip to CID.zip and store in bucket: https://assets.envited-x.net/Asset-CID
* Store *registeredUser* information in bucket: https://metadata.envited-x.net/Asset-CID
* Calculate CIDs with local function of all *publicUser* data
* Create copy of manifest.json named tzip21_asset_manifest.json for replacing relative paths with URLs including ipfs://Data-CIDs
* Remark: An integration/sanity check shall check if the local CID function returns the same result as the Pinata upload function

### (3) Preview data
* TBD

### (4) Mint token
* Upload "publicUser" information to IPFS -> links SHALL be the same as already in tzip21_asset_manifest.json
* Upload  tzip21_asset_manifest.json t IPFS
* Create tzip21_token_metadata.json -> fill information according to mapping table
* Mint token

### (5) Listener
* Catch event with indexer -> store information in permanent DB
* Download *publicUser* information from IPFS to bucket: https://ipfs.envited-x.net/Asset-CID

## Database synchronization

The CID is the connecting identifier between all systems. If additional non-public information needs to be stored in the permanent DB that can only be known before the mint then the CID may help to associate information. An additional unique ID is needed that is know pre-mint.

We get an asset.zip and SHALL have no control its content. We do not know if it has been uploaded before and it is not the concern of the federator.

We create the [tzip21_asset_manifest.json](https://github.com/ASCS-eV/smart-contracts/blob/main/contracts/marketplace/metadata/tzip21_asset_manifest.json) and have sole control over it. We can use this id to create a link between the token metadata and the ENVITED-X DB:

```json
"@id": "did:web:registry.gaia-x.eu:Manifest:ZNh9Z-tHQpkpxJhNobhUVmauYxrfTAZdQy9L",
```

This *@id* SHALL be replaced with a UUID for our DB:

```json
"@id": "urn:uuid:cf1f329d-9c4c-458e-ba0a-a762a296b79c",
```

The following combination makes it secure to sync between the smart contract and the DB:
1) The contract DID (current Ghostnet contract):` did:tezos:NetXnHfVqm9iesp:KT1XC2fTBNqoafnrhEb7TuToRCzewgbHAhnA`
2) The user DID is known on MINT
3) The transaction signature validates against user did
4) The tzip21 asset_manifest id: `"@id": "urn:uuid:cf1f329d-9c4c-458e-ba0a-a762a296b79c"`

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
