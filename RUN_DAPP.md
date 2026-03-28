# Run the Charity dApp Locally (Windows)

This guide runs the app against a local Hardhat network.

## 1) Install dependencies

Open PowerShell in the project root and run:

```powershell
npm install --legacy-peer-deps
```

## 2) Configure env

Make sure `.env.local` contains at least:

```dotenv
NEXT_PUBLIC_RPC_URL=http://127.0.0.1:8545
NEXT_PUBLIC_FORCE_LOCAL_SIGNER=true
NEXT_PUBLIC_PROJECT_ID=<your_walletconnect_project_id>
NEXT_PUBLIC_PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=<any-random-long-secret>
```

Generate `NEXTAUTH_SECRET` quickly:

```powershell
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

## 3) Start local blockchain

In Terminal A:

```powershell
npx hardhat node
```

If using MetaMask mobile on same Wi-Fi, start with LAN binding:

```powershell
npx hardhat node --hostname 0.0.0.0
```

## 4) Deploy contract to localhost

In Terminal B:

```powershell
npx hardhat run scripts/deploy.js --network localhost
npx hardhat run scripts/seed.js --network localhost
```

This updates `contracts/contractAddress.json` with your local contract address.

## 5) Start web app

In Terminal C:

```powershell
npm run dev
```

Open:

- http://localhost:3000

---

## MetaMask setup

### Desktop extension (recommended)

Add network:
- Network name: `Hardhat Local`
- RPC URL: `http://127.0.0.1:8545`
- Chain ID: `31337`
- Symbol: `ETH`

Import Hardhat funded account #0 private key:

`0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80`

### Mobile app

Use your PC LAN IP instead of localhost:
- RPC URL: `http://<YOUR_PC_LAN_IP>:8545`
- Chain ID: `31337`

Keep phone and PC on same Wi-Fi.

---

## Common errors and fixes

### `insufficient funds ... sender balance is 0`
Your signer address has no local ETH.

- Ensure selected MetaMask account is funded on chain `31337`.
- Or fund address from account #0:

```powershell
node -e "const { ethers } = require('ethers'); (async () => { const p=new ethers.JsonRpcProvider('http://127.0.0.1:8545'); const w=new ethers.Wallet('0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80',p); const to='0xYOUR_ADDRESS'; const tx=await w.sendTransaction({to,value:ethers.parseEther('10')}); await tx.wait(); console.log('funded',to); })();"
```

### `could not decode result data ... getCharities`
Contract address/network mismatch.

- Keep Hardhat node running.
- Re-run deploy command.
- Confirm app RPC is `http://127.0.0.1:8545`.

### MetaMask `could not fetch chain id`
- Node is not running, or wrong RPC URL.
- Verify:

```powershell
$payload=@{jsonrpc='2.0';method='eth_chainId';params=@();id=1}|ConvertTo-Json -Compress; Invoke-RestMethod -Uri 'http://127.0.0.1:8545' -Method Post -ContentType 'application/json' -Body $payload | ConvertTo-Json -Compress
```

Expected result contains `0x7a69` (chain `31337`).

---

## Minimal restart flow (when things break)

1. Stop all app/node terminals.
2. Start `npx hardhat node`.
3. Run deploy + seed.
4. Run `npm run dev`.
5. Refresh browser and reconnect wallet.
