# ARC Testnet Automation Scripts

## 🇬🇧 English

Automated scripts for **ARC L1 Testnet** to manage tokens, perform swaps, deploy contracts, register random names, and mint NFTs.

### Features

* **Token Swap:** Random token selection (SRAC, RACS, SACS, DOGG) and amount (0–0.05), with balance check and slippage protection.
* **Approve Tokens:** Automatic allowance setup for swap contracts.
* **Deploy Contracts:** Send ETH to Onchaingm contract.
* **Register Names:** Generate and register random names on-chain.
* **Mint NFTs:** Batch minting on random contracts with network error handling.
* **Multi-account support:** Work sequentially or randomly with multiple accounts.

### Usage

```ts
import { swapRandomToken } from './scripts/swapDefiOnArc';
await swapRandomToken('accountName');

import { deployOnchaingm } from './scripts/deploys';
await deployOnchaingm('accountName', '0.5');

import { infinityName } from './scripts/mintDomains';
await infinityName('accountName', '0.1');

import { mintNft } from './scripts/mintNft';
await mintNft('accountName');
```

### Setup

```bash
npm install
```

Configure `config.ts` with RPC URLs and account names. Use `withPrivateKey` for private key management.

---

## 🇷🇺 Русский

Набор скриптов для **ARC L1 Testnet** для управления токенами, свапов, деплоя контрактов, регистрации случайных имён и mint NFT.

### Возможности

* **Свап токенов:** Случайный выбор токенов (SRAC, RACS, SACS, DOGG) и суммы (0–0.05), проверка баланса и slippage.
* **Аппрув токенов:** Автоматическая установка allowance для контрактов свапов.
* **Деплой контрактов:** Отправка ETH на Onchaingm контракт.
* **Регистрация имён:** Генерация и регистрация случайных имён on-chain.
* **Mint NFT:** Массовый mint на случайные контракты с обработкой ошибок сети.
* **Поддержка нескольких аккаунтов:** Работа последовательно или случайным образом.

### Использование

```ts
import { swapRandomToken } from './scripts/swapDefiOnArc';
await swapRandomToken('accountName');

import { deployOnchaingm } from './scripts/deploys';
await deployOnchaingm('accountName', '0.5');

import { infinityName } from './scripts/mintDomains';
await infinityName('accountName', '0.1');

import { mintNft } from './scripts/mintNft';
await mintNft('accountName');
```

### Установка

```bash
npm install
```

Настрой `config.ts` с RPC URL и аккаунтами. Для приватных ключей используйте `withPrivateKey`.

---

