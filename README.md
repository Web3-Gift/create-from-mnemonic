# create-from-mnemonic
我理解你想要为每一个新生成的助记词创建一个新的 Ethereum 钱包，包括地址和私钥。以下是一个示例 Python 代码，它使用了 bip-utils 和 mnemonic 库生成新的助记词，并为每一个助记词生成对应的 Ethereum 钱包。
from bip_utils import Bip44, Bip44Coins, Bip44Changes
from mnemonic import Mnemonic
import pandas as pd

# 设定生成数量
wallet_amount = 51
wallet_data = []  # 初始化数据，设置header

for i in range(wallet_amount):
    # 生成新的助记词
    mnemo = Mnemonic("english")
    mnemonic = mnemo.generate(128)

    bip_obj_mst = Bip44.FromMnemonic(mnemonic, Bip44Coins.ETHEREUM)
    bip_obj_acc = bip_obj_mst.Purpose().Coin().Account(0)
    bip_obj_chain = bip_obj_acc.Change(Bip44Changes.CHAIN_EXT)
    bip_obj_addr = bip_obj_chain.AddressIndex(0)

    address = bip_obj_addr.PublicKey().ToAddress()
    privateKey = bip_obj_addr.PrivateKey().Raw().ToHex()

    wallet_data.append({"Mnemonic": mnemonic, "Wallet": f"wallet-{i}", "Address": address, "PrivateKey": privateKey})

# 将数据保存为CSV文件
df = pd.DataFrame(wallet_data)
df.to_csv('wallets.csv', index=False)
