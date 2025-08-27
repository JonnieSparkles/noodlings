# Creating Permanent NFTs with Arweave + AR.IO  

## 1. Why Permanent NFTs Matter  
Most NFTs today store their media files on centralized servers or semi-permanent networks like IPFS. These solutions rely on someone continuing to “pin” or pay for storage. If that stops, the NFT may point to a file that no longer exists.  

**A permanent NFT** solves this by ensuring:  

- **Permanent asset storage** – media and metadata live forever on **Arweave**, secured by its storage endowment.  
- **Permanent ownership** – blockchains like Ethereum, Polygon, or Solana record the transfer of ownership permanently.  
- **Permanent access** – the **AR.IO gateway network** and **Wayfinder protocol** ensure data can always be retrieved from multiple sources, independent of any single server.  
- **Verifiability** – anyone can independently confirm that the NFT’s data hasn’t changed by resolving it with `ar://` links through Wayfinder.  

---

## 2. Core Concepts  

### 2.1 Arweave Storage  
- Pay once, store forever.  
- Miners are incentivized to keep multiple replicas available via **Succinct Proofs of Access**.  

### 2.2 Transaction IDs (TxIDs)  
- Every file uploaded gets a **TxID** (a permanent reference).  
- TxIDs are immutable — they never change or expire.  

### 2.3 Manifests  
- Manifests (ANS-104) act like a “folder” on the permaweb.  
- They allow you to access files by name, e.g.:  
  ```
  https://arweave.net/<manifest_TxID>/1.png
  https://arweave.net/<manifest_TxID>/metadata/1.json
  ```  
- Essential for NFT collections (images + JSON metadata).  

### 2.4 Bundling  
- Bundlers like **Turbo** group many uploads into one transaction.  
- Keeps costs extremely low and enables fast, scalable writes.  

### 2.5 Wander (Wallet)  
- Formerly ArConnect.  
- A browser extension wallet for signing and funding Arweave transactions.  

### 2.6 Arweave Name System (ArNS)  
- Human-readable names for NFTs and collections.  
- Can be leased or permanently purchased.  
- Optional, but improves accessibility (e.g., `ar://myproject`) and allows for mutable references in immutable contracts.  

### 2.7 Wayfinder Protocol  
- Resolves `ar://` links across gateways.  
- Enables verifiable retrieval — no need to trust a single server.  

## 2.8 The `ar://` Protocol

- `ar://` is a **gateway-agnostic URI scheme** for referencing Arweave data.
- Instead of hardcoding `https://arweave.net/<TxID>`, you can reference: `ar://<TxID> ar://<manifest_TxID>/1.json`
- Benefits:
    - **Gateway independence** – works across any AR.IO gateway.
	- **Wayfinder compatibility** – ensures retrieval can be verified from multiple sources, not just one server.
	- **Future-proof** – as ecosystem adoption grows, `ar://` will become the standard reference for permanent data.

### Best Practice for Today

- Since `ar://` is still in early adoption, **use both forms** when publishing NFTs:
	- **`ar://` link** → for future-proof, gateway-independent access.
	- **`https://arweave.net/...` link** → for compatibility with existing marketplaces and apps.

**Example (metadata JSON):**

```json
{   
	"name": "Clone #1",   
	"description": "An NFT from the Nerdy Coda Clone series",   
	"image": "ar://<image_manifest_TxID>/1.png",   
	"properties": {     
		"compatibility_fallback": "https://arweave.net/<image_manifest_TxID>/1.png"
	} 
}
```

---

## 3. Tools You’ll Use  
- **Wander** → wallet to hold AR and sign transactions.  
- **ArDrive** → user-friendly app to upload images, metadata, and manifests.  
- **Turbo** → bundling service for fast, cheap uploads.  
- **ViewBlock (or any AR.IO gateway)** → verify TxIDs and manifests.  
- **Wayfinder** → resolve and check NFT data with `ar://` links.  
- **ArNS (optional)** → assign a friendly permanent name to collections.  

---

## 4. Step-by-Step: Creating a Permanent NFT  

### Step 1. Prepare Wallet + AR  
- Install **Wander** wallet.  
- Secure your seed phrase.  
- Fund your wallet with AR tokens (buyable on exchanges like Binance, Gate.io, etc.) or top up with an uploading service like ArDrive Turbo which accepts multiple currencies.  

### Step 2. Upload Assets (Images, Video, JSON)  
- Open **ArDrive**.  
- Create a new public drive and organize folders:  
  - `/images` → NFT artwork  
  - `/metadata` → JSON metadata  
- Upload your files. Costs are based on file size (fractions of a cent for small files).  

### Step 3. Create a Manifest  
- In ArDrive, create a **Manifest File** for `/images`.  
	- Click New>Advanced>Manifest and navigate the the images folder
- This groups all image TxIDs into one entry point.  
- Example usage:  
  ```
  https://arweave.net/<manifest_TxID>/1.png
  https://arweave.net/<manifest_TxID>/25.png
  ```  

### Step 4. Update Metadata JSON  
- Each NFT’s JSON should point to the Images manifest path:  
  ```json
  {
    "name": "Clone #1",
    "description": "An NFT from the Nerdy Coda Clone series",
    "image": "https://arweave.net/<image_manifest_TxID>/1.png"
  }
  ```  
- Scripts can batch-update thousands of JSON files automatically.  

### Step 5. Upload Metadata + Create Manifest  
- Upload all metadata JSON files into `/metadata`.  
- Create a **Manifest File** for `/metadata`.
  - Click New>Advanced>Manifest and navigate to the Metadata folder
- This gives you a single **metadata manifest TxID**.  

### Step 6. Point Your Smart Contract  
- In your NFT smart contract (ERC-721/1155), update the **baseURI** to:  
  ```
  https://arweave.net/<metadata_manifest_TxID>/
  ```  
- Each token will automatically resolve to its JSON file:  
  - Token #1 → `<manifest_TxID>/1.json`  
  - Token #2 → `<manifest_TxID>/2.json`  

### Step 7. Verify in the Marketplace  
- Refresh metadata in OpenSea or your marketplace of choice.  
- Confirm that assets now load from **Arweave**, not IPFS or centralized storage.  
- Buyers can check via **ViewBlock** or **Wayfinder**.  

---

## 5. Workflow Summary (Text Diagram)  

```
Assets (images, video, JSON)
        ↓
Upload via ArDrive (uses Turbo for bundling)
        ↓
Create Manifest (TxID entry point for assets)
        ↓
Update Metadata JSON → point to manifest paths
        ↓
Upload Metadata + Create Manifest
        ↓
Smart Contract (setBaseURI to metadata manifest TxID)
        ↓
Marketplace (OpenSea, etc.) → Verify with Wayfinder/ViewBlock
```  

---

## 6. Best Practices  
- **Always use TxIDs or manifests** as permanent references.  
- **Don't ArDrive share links directly** — they’re convenience UI links, not permanent identifiers.  
- **Test manifest paths** in a browser before updating your contract.  
- **Keep images and metadata separate** in organized folders.  
- For collections, use manifests at both levels (`/images`, `/metadata`).  
- Consider registering an **ArNS name** for user-friendly access (e.g., `gallery.ar.io`).  

---

## 7. Advanced Use Cases  
- **Dynamic NFTs** → combine permanent storage with AO processes for NFTs that evolve.  
- **Cross-chain NFTs** → mint on Ethereum, Solana, or Polygon while storing assets permanently on Arweave.  
- **Identity integration** → use **Primary Names (ArNS)** to tie collections to creator identities.  

---

## 8. Conclusion  
Permanent NFTs aren’t just collectibles — they’re **durable digital artifacts**. By storing assets on **Arweave**, bundling them with **Turbo**, verifying them through **Wayfinder**, and optionally naming them with **ArNS**, creators ensure their NFTs are accessible and verifiable **forever**.  

This approach upgrades NFTs from temporary hype to **permanent cultural records** on the permaweb.  
