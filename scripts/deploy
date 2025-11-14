import { ethers } from "ethers";
import { RPC, serviceName } from "../../config";
import { withPrivateKey } from "../../keychain/getKey";

const contractAddress = "0xa3d9Fbd0edB10327ECB73D2C72622E505dF468a2";

// ABI для метода deploy()
const abi = [
  {
    name: "deploy",
    type: "function",
    inputs: [],
    outputs: [],
    stateMutability: "payable"
  }
];

export async function deployOnchaingm(accountName: string, amountUSDC = "1") {
  return await withPrivateKey(serviceName, accountName, async (pkClean) => {
    const provider = new ethers.JsonRpcProvider(RPC.arc);
    const wallet = new ethers.Wallet(pkClean, provider);
    
    // Подключаем контракт с ABI
    const contract = new ethers.Contract(contractAddress, abi, wallet);

    // Сумма в wei
    const value = ethers.parseEther(amountUSDC);

    console.log(`${accountName} | Deploy | Onchaingm`);

    // Отправка
    const tx = await contract.deploy({ value });
    console.log(`✅ TxHash: ${tx.hash}`);

    const receipt = await tx.wait();
    console.log(`🎉 Подтверждено в блоке: ${receipt.blockNumber}`);
  });
}

// Пример вызова
deployOnchaingm("nameAccount").catch(console.error);
