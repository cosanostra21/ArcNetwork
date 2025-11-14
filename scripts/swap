import { ethers } from "ethers";
import { RPC, serviceName } from "../../config";
import { withPrivateKey } from "../../keychain/getKey";
import { sleep } from "../../functions"


// --------------------------
// Константы
// --------------------------
const ZERO_ADDRESS = "0x3600000000000000000000000000000000000000";
const SWAP_CONTRACT_ADDRESS = "0x284C5Afc100ad14a458255075324fA0A9dfd66b1";

// ERC20 ABI для аппрува и баланса
const ERC20_ABI = [
  "function approve(address spender, uint256 amount) external returns (bool)",
  "function allowance(address owner, address spender) external view returns (uint256)",
  "function balanceOf(address owner) view returns (uint256)"
];

// --------------------------
// Типы токенов
// --------------------------
type SwapToken = { address: string; decimals: number; rate: number; swapContract: string };
type UsdcToken = { address: string; decimals: number };

// --------------------------
// Токены
// --------------------------
const SWAP_TOKENS: Record<string, SwapToken> = {
  SRAC: { address: "0x49cd69442dB073E7b94B0124e316AB7C68b95988", decimals: 18, rate: 0.632490, swapContract: "0xd065a783C362d73b5a62EfB2B2e5DEDe49d16aa3" },
  RACS: { address: "0x6E63e2cABECCe5c3A1c37b79A958a9542076A1e3", decimals: 18, rate: 1.277780, swapContract: "0x55b9CbF50dc1171526635d03267f4A0979f1fFBb" },
  SACS: { address: "0x63F856fBAB3535174bFaFD6EFd720C634d6FD458", decimals: 18, rate: 1.738230, swapContract: "0xFDC1F922f7065602F06504d62E314A1cEe72c189" },
  DOGG: { address: "0x832Fa4331050045F911bD6d59EAD144A04B965cB", decimals: 18, rate: 151.114000, swapContract: "0xa7Eb57a1538BC8ca2F0452954610A7d6f1F0242B" },
};

const USDC: UsdcToken = { address: "0x3600000000000000000000000000000000000000", decimals: 6 };

// --------------------------
// Аппрув токена
// --------------------------
async function approveToken(accountName: string, tokenAddress: string, spender: string) {
  await withPrivateKey(serviceName, accountName, async (pkClean) => {
    const provider = new ethers.JsonRpcProvider(RPC.arc);
    const wallet = new ethers.Wallet(pkClean, provider);
    const tokenContract = new ethers.Contract(tokenAddress, ERC20_ABI, wallet);

    const allowanceRaw = await tokenContract.allowance(wallet.address, spender);
    const allowance = typeof allowanceRaw === 'bigint' ? allowanceRaw : BigInt(allowanceRaw.toString());

    if (allowance >= ethers.MaxUint256 / 2n) return;

    console.log(`🚀 Approving ${tokenAddress}...`);
    const tx = await tokenContract.approve(spender, ethers.MaxUint256);
    await tx.wait();
    console.log(`✅ Approved ${tokenAddress}`);
  });
}

// --------------------------
// Преобразование в 32 байта hex
// --------------------------
function to32ByteHex(value: bigint) {
  const hex = value.toString(16);
  if (hex.length > 64) throw new Error("Value too big for 32 bytes");
  return "0".repeat(64 - hex.length) + hex;
}

// --------------------------
// Swap USDC → токен
// --------------------------
async function swapUsdcToToken(accountName: string, tokenSymbol: keyof typeof SWAP_TOKENS, minAmount = 0.01, maxAmount = 0.05, slippagePercent = 3) {
  const token = SWAP_TOKENS[tokenSymbol];
  await withPrivateKey(serviceName, accountName, async (pkClean) => {
    const provider = new ethers.JsonRpcProvider(RPC.arc);
    const wallet = new ethers.Wallet(pkClean, provider);
    const addrNo0x = wallet.address.slice(2);

    const amountUSDC = Math.random() * (maxAmount - minAmount) + minAmount;
    const amountIn = BigInt(Math.round(amountUSDC * 10 ** USDC.decimals));
    const amountOut = amountUSDC * token.rate * (1 - slippagePercent / 100);
    const amountOutBigInt = BigInt(Math.round(amountOut * 10 ** token.decimals));

    await approveToken(accountName, USDC.address, token.swapContract);

    const data =
      "0x84b065d3" +
      to32ByteHex(amountIn) +
      to32ByteHex(amountOutBigInt) +
      "0".repeat(24) +
      addrNo0x;

    const tx = await wallet.sendTransaction({
      to: token.swapContract,
      value: 0,
      data,
      gasLimit: 500_000
    });

    console.log(`💰 Swap USDC → ${tokenSymbol}: ${amountUSDC} USDC → ${amountOut.toFixed(6)} ${tokenSymbol}`);
    await tx.wait();
    console.log("✅ Swap выполнен:", tx.hash);
  });
}

// --------------------------
// Рандомный swap токен → токен
// --------------------------
async function swapRandomToken(accountName: string, slippagePercent = 3) {
  await withPrivateKey(serviceName, accountName, async (pkClean) => {
    const provider = new ethers.JsonRpcProvider(RPC.arc);
    const wallet = new ethers.Wallet(pkClean, provider);

    const tokenSymbols = Object.keys(SWAP_TOKENS) as (keyof typeof SWAP_TOKENS)[];
    if (tokenSymbols.length < 2) return;

    let tokenInSymbol = tokenSymbols[Math.floor(Math.random() * tokenSymbols.length)];
    let tokenOutSymbol: keyof typeof SWAP_TOKENS;
    do {
      tokenOutSymbol = tokenSymbols[Math.floor(Math.random() * tokenSymbols.length)];
    } while (tokenOutSymbol === tokenInSymbol);

    const tokenIn = SWAP_TOKENS[tokenInSymbol];
    const tokenOut = SWAP_TOKENS[tokenOutSymbol];

    const tokenInContract = new ethers.Contract(tokenIn.address, ERC20_ABI, wallet);
    const balanceRaw = await tokenInContract.balanceOf(wallet.address);
    const balance = typeof balanceRaw === "bigint" ? balanceRaw : BigInt(balanceRaw.toString());

    const amountHuman = Math.random() * 0.05;
    const amountIn = BigInt(Math.round(amountHuman * 10 ** tokenIn.decimals));
    if (balance < amountIn) {
      console.log(`⚠️ Недостаточно ${tokenInSymbol}. Баланс: ${balance}, требуется: ${amountIn}`);
      return;
    }

    const rate = tokenIn.rate / tokenOut.rate;
    const amountOut = amountHuman * rate * (1 - slippagePercent / 100);
    const amountOutBigInt = BigInt(Math.round(amountOut * 10 ** tokenOut.decimals));
    const path = [tokenIn.address, ZERO_ADDRESS, tokenOut.address];

    await approveToken(accountName, tokenIn.address, SWAP_CONTRACT_ADDRESS);

    const swapContract = new ethers.Contract(SWAP_CONTRACT_ADDRESS, [
      "function swapExactTokensForTokens(uint256 amountIn, uint256 amountOutMin, address[] path, address to)"
    ], wallet);

    console.log(`💰 Swapping ${tokenInSymbol} → ${tokenOutSymbol}, amountIn: ${amountIn}, minOut: ${amountOutBigInt}`);
    const tx = await swapContract.swapExactTokensForTokens(amountIn, amountOutBigInt, path, wallet.address, { gasLimit: 500_000 });
    await tx.wait();
    console.log("✅ Swap выполнен:", tx.hash);
  });
}

// --------------------------
// Пример вызова
// --------------------------
(async () => {
  const account = "nameAccount";
  await sleep(2000);

  await swapUsdcToToken(account, "RACS");
  await sleep(2000);
  await swapRandomToken(account);
})();
