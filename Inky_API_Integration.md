# Inky Factory API Integration Guide

Welcome to the Inky Factory API! This guide will help you integrate token deployment and metadata features from Inky Factory into your own application, specifically for the **Ink (Ethereum Layer 2) network**.

---

## API Base URLs

- **Token Launch API:**  
  `POST https://t6igafld4b.execute-api.us-east-2.amazonaws.com/launch`

- **Token Metadata API:**  
  `GET https://gtsbz29g0f.execute-api.us-east-2.amazonaws.com/prod/recent_tokens`

---

## Table of Contents
- [Overview](#overview)
- [Endpoints](#endpoints)
- [Token Launch API](#token-launch-api)
- [Token Metadata API](#token-metadata-api)
- [Image Upload & Resizing](#image-upload--resizing)
- [Sample Integration Flow](#sample-integration-flow)
- [Best Practices & Recommendations](#best-practices--recommendations)
- [Support](#support)

---

## Overview
Inky Factory enables:
- One-click token launches on the Ink (Ethereum Layer 2) network
- Custom branding and metadata for each token
- Real-time token metadata and stats for the Ink ecosystem

You can:
- Launch new tokens programmatically from your app, including image/logo uploads
- Fetch all Ink network token metadata for display or analytics

---

## Endpoints

### 1. **Token Launch API**
- **Endpoint:** `POST https://t6igafld4b.execute-api.us-east-2.amazonaws.com/launch`
- **Description:** Deploy a new token on Ink and register its metadata (including logo and cover photo)
- **Content-Type:** `application/json`

### 2. **Token Metadata API**
- **Endpoint:** `GET https://gtsbz29g0f.execute-api.us-east-2.amazonaws.com/prod/recent_tokens`
- **Description:** Returns all tokens deployed via Inky Factory on the Ink network, with full metadata.

---

## Token Launch API

**Request:**
```http
POST https://t6igafld4b.execute-api.us-east-2.amazonaws.com/launch
Content-Type: application/json
```

**Required Fields:**
- `name` (string): Token name
- `symbol` (string): Token symbol (ticker)
- `logo` (string): Base64-encoded PNG or JPEG (see [Image Upload & Resizing](#image-upload--resizing))

**Optional Fields:**
- `cover_photo` (string): Base64-encoded PNG or JPEG (banner)
- `description` (string): Project description
- `website` (string): Project website URL
- `twitter` (string): X/Twitter URL
- `telegram` (string): Telegram URL
- `discord` (string): Discord URL

**Sample Request:**
```json
{
  "name": "MyToken",
  "symbol": "MTK",
  "logo": "<base64 string>",
  "cover_photo": "<base64 string>",
  "description": "A cool new token",
  "website": "https://mytoken.com",
  "twitter": "https://twitter.com/mytoken",
  "telegram": "https://t.me/mytoken",
  "discord": "https://discord.gg/mytoken"
}
```

**Sample Response:**
```json
{
  "status": "success",
  "contract_address": "0x...",
  "logo_url": "https://.../logo.png",
  "cover_photo_url": "https://.../cover.png",
  "message": "Token deployed successfully"
}
```

---

## Token Metadata API

**Request:**
```http
GET https://gtsbz29g0f.execute-api.us-east-2.amazonaws.com/prod/recent_tokens
```

**Response:**
```json
[
  {
    "contract_address": "0x...",
    "name": "MyToken",
    "symbol": "MTK",
    "logo_url": "https://.../logo.png",
    "cover_photo_url": "https://.../cover.png",
    "description": "A cool new token",
    "website": "https://mytoken.com",
    "twitter": "https://twitter.com/mytoken",
    "telegram": "https://t.me/mytoken",
    "discord": "https://discord.gg/mytoken",
    "network": "ink",
    "timestamp": 1710000000
  },
  ...
]
```

---

## Image Upload & Resizing

**To ensure fast uploads and optimal display, you must resize images before sending them to the API.**

- **Logo:** Max 100x100 pixels (PNG or JPEG recommended)
- **Cover Photo:** 1360x320 pixels (PNG or JPEG recommended)
- **Convert images to base64-encoded strings** before including in the JSON payload.

**Sample JavaScript (browser) image resize function:**
```js
function resizeImage(file, maxWidth, maxHeight, callback) {
  const reader = new FileReader();
  reader.onload = function(e) {
    const img = new Image();
    img.onload = function() {
      const canvas = document.createElement('canvas');
      let width = img.width;
      let height = img.height;
      if (width > maxWidth || height > maxHeight) {
        if (width / height > maxWidth / maxHeight) {
          height = Math.round((height * maxWidth) / width);
          width = maxWidth;
        } else {
          width = Math.round((width * maxHeight) / height);
          height = maxHeight;
        }
      }
      canvas.width = width;
      canvas.height = height;
      canvas.getContext('2d').drawImage(img, 0, 0, width, height);
      callback(canvas.toDataURL('image/png').split(',')[1]); // base64 string
    };
    img.src = e.target.result;
  };
  reader.readAsDataURL(file);
}
```

---

## Sample Integration Flow

1. **User fills out token info and uploads images in your app.**
2. **Resize images on the frontend** (see above) and convert to base64.
3. **POST all data to the Token Launch API** as shown above.
4. **Display success/failure to the user.**
5. **Fetch and display token metadata using the Token Metadata API** for live stats and discovery.

---

## Displaying Inky Factory Tokens in Your App

If you want to display all tokens created via Inky Factory in your application (for example, in a trading interface, token explorer, or analytics dashboard), you can use the **Token Metadata API** to fetch and sync the full list of tokens and their metadata for the Ink network.

### **How to Fetch All Tokens**
- **Endpoint:** `GET https://gtsbz29g0f.execute-api.us-east-2.amazonaws.com/prod/recent_tokens`
- **Returns:** All tokens deployed via Inky Factory on the Ink network, with full metadata (name, symbol, contract address, logo, cover photo, description, social links, etc.)

**Example Request:**
```http
GET https://gtsbz29g0f.execute-api.us-east-2.amazonaws.com/prod/recent_tokens
```

**Example Response:**
```json
[
  {
    "contract_address": "0x...",
    "name": "MyToken",
    "symbol": "MTK",
    "logo_url": "https://.../logo.png",
    "cover_photo_url": "https://.../cover.png",
    "description": "A cool new token",
    "website": "https://mytoken.com",
    "twitter": "https://twitter.com/mytoken",
    "telegram": "https://t.me/mytoken",
    "discord": "https://discord.gg/mytoken",
    "network": "ink",
    "timestamp": 1710000000
  },
  ...
]
```

### **Best Practices for Displaying Tokens**
- **Sync regularly:** Query the Token Metadata API endpoint periodically to keep your token list up to date.
- **Display metadata:** Show token name, symbol, logo, cover photo, and social links for a rich user experience.
- **Link to contract explorers:** Use the `contract_address` to link to InkScan or other Ink L2 explorers.

### **Use Case: EVM Trading App**
By integrating this endpoint, your EVM trading app (such as the one owned by Kraken) can:
- Display all Inky Factory tokens for trading, analytics, or discovery
- Show live token stats, branding, and social links
- Enable users to search, filter, and interact with the full Inky Factory ecosystem

**This enables seamless ecosystem integration and ensures your users always have access to the latest tokens launched via Inky Factory on Ink.**

---

## Best Practices & Recommendations
- **Validate all required fields** before sending requests.
- **Resize and compress images** to keep payloads small (<2MB per image recommended).
- **Handle API errors gracefully** and display clear messages to users.
- **Cache token metadata** if you expect high traffic.
- **Contact us for API keys or higher rate limits** if you plan to launch at scale.

---

## Support
For questions, support, or partnership requests, contact DeployerOne on https://x.com/DeployerOne 