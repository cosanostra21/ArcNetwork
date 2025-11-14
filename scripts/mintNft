import { ethers } from "ethers";
import { RPC, serviceName } from "../../config";
import { withPrivateKey } from "../../keychain/getKey"; 
import { sleep } from "../../functions";

const contractList = [
  { name: "Lady", address: "0xbab687216f462ba3971634ff6f41A11210F15d80" },
  { name: "Dusk", address: "0x4eeEf4b00b88A42532cD3016528AD18D2EEdBF6a" },
  { name: "Cyan", address: "0x5cDFcf04883487EB9F80840e8b05391e21B79e8A"},
  { name: "Camellia", address: "0x8DF9a64595E4b0b0f5C12fB92F88765a52E63a0f"}
];

function getRandomContract() {
  const randomIndex = Math.floor(Math.random() * contractList.length);
  return contractList[randomIndex];
}


async function mintNft(accountName: string) {
  return await withPrivateKey(serviceName, accountName, async (pkClean) => {
    let provider;
    try {
      provider = new ethers.JsonRpcProvider(RPC.megaeth);
    } catch (err: any) {
      console.warn(`⚠️ Провайдер не подключился (${accountName}): ${err.message}`);
      console.log("⏭ Пропуск аккаунта, продолжаем...\n");
      return; 
    }

    const wallet = new ethers.Wallet(pkClean, provider);
    const addrNo0x = wallet.address.slice(2);
    
    const txCount = Math.floor(Math.random() * 6) + 5; // 5–10 транзакций
    console.log(`\n🔐 Аккаунт: ${accountName} — ${txCount} транзакций`);

    for (let i = 0; i < txCount; i++) {
      const contract = getRandomContract();

      const data =
        "0x84bb1e42000000000000000000000000" + addrNo0x +
        "0000000000000000000000000000000000000000000000000000000000000001" +
        "000000000000000000000000eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee" +
        "0000000000000000000000000000000000000000000000000000000000000000" +
        "00000000000000000000000000000000000000000000000000000000000000c0" +
        "0000000000000000000000000000000000000000000000000000000000000160" +
        "0000000000000000000000000000000000000000000000000000000000000080" +
        "0000000000000000000000000000000000000000000000000000000000000000" +
        "ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff" +
        "0000000000000000000000000000000000000000000000000000000000000000" +
        "0000000000000000000000000000000000000000000000000000000000000000" +
        "0000000000000000000000000000000000000000000000000000000000000000" +
        "0000000000000000000000000000000000000000000000000000000000000000";



      const maxRetries = 3;
      for (let attempt = 1; attempt <= maxRetries; attempt++) {

        try {
          const tx = await wallet.sendTransaction({
            to: contract.address,
            data: data,
          });

          console.log(`Mint | ${contract.name} | ${accountName} | Tx hash: ${tx.hash}`);
          const receipt = await tx.wait();
          console.log(`Status | ${receipt?.status === 1 ? "Transaction Confirmed" : "Error!"}`);
          break;
        } catch (err: any) {

          const message = err.message || "";
          if (
            message.includes("ECONNRESET") ||
            message.includes("504") ||
            message.includes("failed to detect network")
          ) {
            if (attempt < maxRetries) {
              console.warn(`⚠️ Сетевая ошибка (${message.trim()}), повтор ${attempt}/${maxRetries} через 3 сек...`);
              await sleep(3000);
              continue;
            } else {
              console.warn(`🚫 Ошибка подключения после ${maxRetries} попыток, пропускаем транзакцию`);
              break;
            }
          } else {
            console.warn(`❌ Ошибка при отправке транзакции: ${message}`);
            break;
          }
        }
      }

      if (i < txCount - 1) {
        const delayMs = Math.floor(Math.random() * (7000 - 4000 + 1)) + 4000; // 4–7 сек
        console.log(`⏱ Задержка перед следующей транзакцией | ${Math.round(delayMs / 1000)} сек`);
        await sleep(delayMs);
      }
    }
  });
}


(async () => {
  const account = "accountName";
  await mintNft(account)
})();


