import { ethers } from "ethers";
import { RPC, serviceName } from "../../config";
import { withPrivateKey } from "../../keychain/getKey";


function getRandomString(minLength = 5, maxLength = 9) {
  const chars = "abcdefghijklmnopqrstuvwxyz";
  const length = Math.floor(Math.random() * (maxLength - minLength + 1)) + minLength;
  let result = "";
  for (let i = 0; i < length; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length));
  }
  return result;
}


async function infinityName(accountName: string, amountUSDC = "1") {
  return await withPrivateKey(serviceName, accountName, async (pkClean) => {
    const provider = new ethers.JsonRpcProvider(RPC.arc);
    const wallet = new ethers.Wallet(pkClean, provider);

    const contractAddress = "0x76a816EFa69e3183972ff7a231F5C8d7b065d9De";

    const abi = [
      "function register(string arg0, address arg1) external payable"
    ];

    const contract = new ethers.Contract(contractAddress, abi, wallet);

    const arg0 = getRandomString();
    const arg1 = "0xF278AC8e97dd418A3ce13307Fa1b44Ff87a18F7c"; // адрес получателя

    const value = ethers.parseEther(amountUSDC);

    // Отправка транзакции
    const tx = await contract.register(arg0, arg1, { value, gasLimit: 500_000 });

    console.log(`✅ Tx отправлена: ${tx.hash}`);
    const receipt = await tx.wait();
    console.log(`🎉 Транзакция подтверждена в блоке ${receipt.blockNumber}`);
  });
}

// 🚀 Запуск
infinityName("nameAccount").catch(console.error);
