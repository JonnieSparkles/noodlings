# Creating Unruggable NFTs with Arweave + AR.IO 
*Permanent, durable, and verifiable digital assets for the permaweb*  

## 1. Why Permanent NFTs Matter  
Most NFTs today store their media files on centralized servers or semi-permanent networks like IPFS. These solutions rely on someone continuing to “pin” or pay for storage. If that stops, the NFT may point to a file that no longer exists. Even if the data still exists somewhere, specific domain links can rot or become inaccessible if a URL shuts down, a project discontinues service, DNS records change, or due to regional restrictions.

**An Unruggable NFT** solves this by ensuring:  

- **Permanent asset storage** – media and metadata live forever on **Arweave**, secured by its storage endowment.  
- **Permanent ownership** – blockchains like Ethereum, Polygon, or Solana record the transfer of ownership permanently.  
- **Permanent access** – the **AR.IO Network** and **Wayfinder protocol** ensure data can always be retrieved from multiple sources, independent of any single server.  
- **Verifiability** – anyone can independently confirm that the NFT’s data hasn’t changed by resolving it with `ar://` links through Wayfinder.  

---

## 2. TL;DR – How to Create an Unruggable NFT  

1. **Upload your assets (images, video, JSON)** to Arweave via **ArDrive**.  
2. **Create a manifest** for `/images` → gives you clean URLs like `https://arweave.net/<manifest_TxID>/1.png`.  
3. **Update your metadata JSON** to point to those image URLs.  
4. **Upload metadata JSON files** into `/metadata` and **create a manifest**.  
5. **Use the metadata manifest TxID** as the `baseURI` in your NFT smart contract.  
6. **Verify** on OpenSea or other marketplaces — assets will load from **Arweave**, permanently.  
7. **Best practice**: publish both `ar://` links (future-proof, gateway-agnostic) and `https://arweave.net/...` links (current compatibility).
8. #BeUnruggable  

---

## 3. Core Concepts  

### 3.1 Arweave Storage  
- Pay once, store forever.  
- Miners are incentivized to keep multiple replicas available via **Succinct Proofs of Access**.  

### 3.2 Transaction IDs (TxIDs)  
- Every file uploaded gets a **TxID** (a permanent reference).  
- TxIDs are immutable - they never change or expire.  

### 3.3 Manifests  
- Manifests (ANS-104) act like a “folder” on the permaweb.  
- They allow you to access files by name, e.g.:  
  ```
  https://arweave.net/<manifest_TxID>/1.png
  https://arweave.net/<manifest_TxID>/metadata/1.json
  ```  
- Essential for NFT collections (images + JSON metadata).  

### 3.4 Bundling  
- Bundlers like **Turbo** group many uploads into one transaction.  
- Keeps costs extremely low and enables fast, scalable writes.  

### 3.5 Wander (Wallet)  
- Formerly ArConnect.  
- A browser extension wallet for signing and funding Arweave transactions.  

### 3.6 Arweave Name System (ArNS)  
- Human-readable names for NFTs and collections.  
- Can be leased or permanently purchased.  
- Optional, but improves accessibility (e.g., `ar://myproject`) and allows for mutable references in immutable contracts.  

### 3.7 Wayfinder Protocol  
- Resolves `ar://` links across gateways.  
- Enables verifiable retrieval - no need to trust a single server.  

## 3.8 The `ar://` Protocol

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

## 4. Tools You’ll Use  
- **Wander** → wallet to hold AR and sign transactions.  
- **ArDrive** → user-friendly app to upload images, metadata, and manifests.  
- **Turbo** → bundling service for fast, cheap uploads.  
- **ViewBlock (or any AR.IO gateway)** → verify TxIDs and manifests.  
- **Wayfinder** → resolve and check NFT data with `ar://` links.  
- **ArNS (optional)** → assign a friendly permanent name to collections.  

---

## 5. Step-by-Step: Creating a Permanent NFT  

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
- Example Manifest (simplified):
 ```json  
{
  "manifest": "arweave/paths",
  "version": "0.2.0",
  "index": {
    "path": "1.png"
  },
  "paths": {
    "1.png": {
      "id": "PLVeIFonJpML_jNL4nHM1hMI714723Ab53tmMwzpYD8"
    },
    "2.png": {
      "id": "w4H-Hv5JNpgF86NlbRDqpnMObOXHVD0U6PIv8PgW7Uo"
    }
  }
 ```  
- This manifest means you can now reference images by name:
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
Example Metadata Manifest (simplified):
 ```json  
{
  "manifest": "arweave/paths",
  "version": "0.2.0",
  "index": {
    "path": "1.json"
  },
  "paths": {
    "1.json": {
      "id": "kwoN-mtS5jEEzsB6xn9breiWuSYb15OMOLfTkL75480"
    },
    "2.json": {
      "id": "yglJ_xpJWMKLug_dBZPQgPYR5AildczqEWfw5I3OTqU"
    }
  }
}
 ```  
- **Your metadata manifest TxID is what you’ll plug into the NFT smart contract as the baseURI.**

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

## 6. Workflow Summary (Text Diagram)  

```
Assets (images, video, JSON)
        ↓
Upload via ArDrive (uses Turbo for bundling)
        ↓
Create Images Manifest (TxID entry point for assets)
        ↓
Update Metadata JSON → point to manifest paths
        ↓
Upload Metadata + Create Metadata Manifest
        ↓
Smart Contract (setBaseURI to metadata manifest TxID)
        ↓
Marketplace (OpenSea, etc.) → Verify with Wayfinder/ViewBlock
```  

---

## 7. Best Practices  
- **Always use TxIDs or manifests** as permanent references.  
- **Avoid using ArDrive share links directly** - they’re convenience UI links, not permanent identifiers.  
- **Test manifest paths** in a browser before updating your contract.  
- **Keep images and metadata separate** in organized folders.  
- For collections, use manifests at both levels (`/images`, `/metadata`).  
- Consider registering an **ArNS name** for user-friendly access (e.g., `gallery.ar.io`).  

---

## 8. Conclusion  
Unruggable NFTs aren’t just collectibles - they’re durable digital artifacts. By storing assets on Arweave, bundling them with Turbo, verifying them through Wayfinder, and optionally naming them with ArNS, creators ensure their NFTs are accessible, verifiable, and resilient across time, gateways, and regions.

This approach upgrades NFTs from temporary hype to permanent cultural records on the permaweb - immune to storage loss, link rot, or gateway shutdowns.

---

## 9. Additional Resources
- [docs.ar.io](https://docs.ar.io)  
- [docs.ardrive.io](https://docs.ardrive.io)
- [HashLips Video Guide](https://www.youtube.com/watch?v=MTSPjmCmdqs)
- [Can you store NFTs on Arweave?](https://ardrive.io/can-you-store-nfts-on-arweave)
- More coming soon^tm
