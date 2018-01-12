# BlockchainPublicRegulationFabric-Food

在这个旅程中，我们通过一个部署在业务网络上的智能合约，获得食品供应商验证计划的合规逻辑。

这个业务网络定义：

**参与者：** `Supplier` `Importer` `Retailer` `Regulator`

**资产：** `ProductListingContract`

**交易：** `createProductListing` `transferListing` `checkProducts` `updateExemptedList`

最初，供应商将把食品移交给进口商，进口商验证供应商、国家和食品类型是否都与正确的标识符匹配。在入境口岸，将供应商与数据库中已知供应商的名单进行核对（由监管机构管理）。如果供应商属于豁免类型，则产品将被转移给零售商。如果供应商属于非豁免类型，则根据数据库中已知食品列表（由监管机构管理）对产品进行检查。如果食品是豁免的产品，然后将其转移给零售商。如果食品是非豁免产品，进口商必须进行危害分析（独立或使用第三方）。供应商向监管机构提供危害分析报告。

当一个`createProductListing`交易被递交时，`createProductListing`函数被调用。该逻辑允许供应商创建一个`ProductListingContract`资产。

当一个`transferListing`交易由`ProductListingContract`所有者递交时，`transferListing`函数被调用。这个递交，要么`Supplier`移交`ProductListingContract`给`Importer`，要么在完成了产品的豁免检查时由`Importer`移交`ProductListingContract`给`Retailer`。

当`Supplier`递交一个`checkProducts`交易时，`checkProducts`函数被调用来对`ProductListingContract`中的产品执行豁免检查。如果`ProductListingContract`合同中所有商品都是免检的，合同状态会变为`CHECKCOMPLETED`，否则合同状态会变为`HAZARDANALYSISCHECKREQ`。`HAZARDANALYSISCHECKREQ`意味着`Supplier`需要为产品提供危害分析报告。递交报告后，`Supplier`执行`checkProducts`交易，来完成对产品的豁免检查。

当`Regulator`递交`updateExemptedList`交易时，`updateExemptedList`函数被调用，用来更新免检组织id和免检产品id的清单。

## 包含的组件

- Hyperledger Fabric

- Hyperledger Composer

## 包括技术

- Blockchain

- 容器

- 云计算

## 应用工作流程图

[![应用程序工作流](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/archi.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/archi.png)

- 安装Hyperledger Composer开发工具

- 配置并启动Hyperledger Fabric网络

- 生成业务网络档案文件

- 使用Composer Playground部署业务网络档案

- （备选方法）在本地运行的Hyperledger Composer上部署业务网络档案

## 步骤

1. [生成业务网络档案（BNA）](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/README.md#1-generate-the-business-network-archive-bna)

2. [用Composer Playground部署业务网络档案](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/README.md#2-deploy-the-business-network-archive-using-composer-playground)

3. [在本地运行的Hyperledger Composer上部署业务网络档案](README.md#3-deploy-the-business-network-archive-on-hyperledger-composer-running-locally)

## 1.生成业务网络档案（BNA）

要检查文件的结构是否有效，现在可以为你的业务网络定义生成业务网络档案（BNA）文件。BNA文件是可部署的单元 - 可以部署到Composer运行时进行执行。

使用以下命令生成网络档案：

```
npm install
```

你应该看到以下输出：

```
Creating Business Network Archive

Looking for package.json of Business Network Definition
	Input directory: /Users/ishan/Documents/git-demo/BlockchainPublicRegulationFabric-Food

Found:
	Description: Sample food supplier verification network
	Name: food-supply
	Identifier: food-supply@0.0.1

Written Business Network Definition Archive file to
	Output file: ./dist/food-supply.bna

Command succeeded
```

`composer archive create`命令会在`dist`文件夹下创建了一个叫`food-supply.bna`的文件。

你可以利用嵌入式运行时测试业务网络定义，该运行时在Node.js进程的内存中存储“区块链”状态。从你的项目工作目录中，打开文件`test/foodTest.js`并运行以下命令：

```bash
npm test
```

你应该看到以下输出：

```
> food-supply@0.0.1 test /Users/ishan/Documents/git-demo/BlockchainPublicRegulationFabric-Food
> mocha --recursive

  FoodSupply - Test
    #FSVP
      ✓ Create Participants (185ms)
      ✓ Transfer ProductListing to Importer
      ✓ Exempt Check for ProductListing (41ms)
      ✓ Transfer ProductListing to Retailer (56ms)

  4 passing (1s)
```

## 2.使用Composer Playground部署业务网络档案

打开 [Composer Playground](http://composer-playground.mybluemix.net/)，默认情况下将导入基本示例网络。如果你之前使用过Playground，请务必通过在浏览器控制台中运行`localStorage.clear()`清除浏览器本地存储。

现在导入`food-supply.bna`文件并点击部署按钮。

[![img](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/importbtn.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/importbtn.png)

> 你也可以在本地安装[Composer Playground](https://hyperledger.github.io/composer/installing/using-playground-locally.html)。

你将看到以下内容：

[![img](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/composerplayground.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/composerplayground.png)

要测试你的业务网络定义，首先单击**测试**选项卡：

在`Supplier`参与者库中，创建一个新的参与者。确保你先点击`Supplier`最左边的标签，然后点击`Create New Participant`按钮。

[![img](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/createparticipantbtn.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/createparticipantbtn.png)

```
{
  "$class": "composer.food.supply.Supplier",
  "supplierId": "supplierA",
  "countryId": "UK",
  "orgId": "ACME"
}
```

同样通过选择相应的选项卡来创建零售商、监管机构、进口商参与者。

```
{
  "$class": "composer.food.supply.Retailer",
  "retailerId": "retailerA",
  "products": []
}
```
```
{
  "$class": "composer.food.supply.Regulator",
  "regulatorId": "regulatorA",
  "location": "SF",
  "exemptedOrgIds": ["ACME","XYZ CORP"],
  "exemptedProductIds": ["prodA","prodB"]
}
```
```
{
  "$class": "composer.food.supply.Importer",
  "importerId": "importerA"
}
```

现在我们准备添加**访问控制**。首先点击`admin`选项卡，向参与者颁发**新的ID**并将ID添加到钱包。请按照以下图片中的说明进行操作：

[![管理员标签](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/admintab.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/admintab.png)

点击 `Issue New Id`按钮来创建新的ID。 [![生成新的ID](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/generateNewId.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/generateNewId.png)

点击`Add to my Wallet`链接将新生成的ID添加到`Wallet`。 [![添加到钱包](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/addtowallet.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/addtowallet.png)

[![Ids到钱包](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/idstowallet.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/idstowallet.png)

选择`Supplier id`来自`Wallet tab`标签。现在点击`test tab`执行`createProductListing`和`transferListing`交易。

[![选择ID](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/selectid.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/selectid.png)

现在点击`Submit Transaction`按钮并从下拉列表中选择`createProductListing`交易，来为产品列表创建一个产品列表。`products`数组元素包含有关`productid`和`quantity`的信息（用逗号`,`分隔）。

```
{
  "$class": "composer.food.supply.createProductListing",
  "products": ["prodA,5","prodB,2"],
  "user": "resource:composer.food.supply.Supplier#supplierA"
}
```

成功执行交易后，`productListing`将在`ProductListingContract`库中创建。

[![产品清单](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/productListing.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/productListing.png)

同样，递交一个`transferListing`交易来将产品列表转移到`Importer`。

> `ProductListingContractID`是从`ProductListingContract`库复制的产品列表合同的ID 。

```
{
  "$class": "composer.food.supply.transferListing",
  "ownerType": "supplier",
  "newOwner": "resource:composer.food.supply.Importer#importerA",
  "productListing": "resource:composer.food.supply.ProductListingContract#<ProductListingContractID>"
}
```

`importerA`将是`ProductListingContract`的所有者，状态将是`EXEMPTCHECKREQ`。另外，产品列表将从`Supplier`视图中移除。现在从`Wallet tab`中选择`importer`id ，递交`checkProducts`交易来对产品执行豁免检查。

```
{
  "$class": "composer.food.supply.checkProducts",
  "regulator": "resource:composer.food.supply.Regulator#regulatorA",
  "productListing": "resource:composer.food.supply.ProductListingContract#<ProductListingContractID>"
}
```

成功执行交易将会将产品列表的状态更改为`CHECKCOMPLETED`。现在执行`transferListing`交易将产品移交给零售商。

```
{
  "$class": "composer.food.supply.transferListing",
  "ownerType": "importer",
  "newOwner": "resource:composer.food.supply.Retailer#retailerA",
  "productListing": "resource:composer.food.supply.ProductListingContract#<ProductListingContractID>"
}
```

交易将改变`ProductListingContract`的所有者，并更新`Retailer`库中的产品列表。从`Wallet tab`中选择`Retailer`ID 并查看更新的库。

[![产品清单](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/retailerPL.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/retailerPL.png)

[![零售商注册](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/raw/master/images/retailer.png)](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/images/retailer.png)

> 你也可以使用默认的`System user`执行所有操作，因为我们在`permissions.acl`中有一个规则来允许`System user`执行所有访问。

## 3.在本地运行的Hyperledger Composer上部署业务网络存档

请按照[说明](https://github.com/IBM/BlockchainNetwork-CompositeJourney#2-starting-hyperledger-fabric)启动本地Fabric 。现在将目录切换到包含`food-supply.bna`文件的`dist`文件夹并输入：

```
cd dist
composer runtime install --card PeerAdmin@hlfv1 --businessNetworkName food-supply
composer network start --card PeerAdmin@hlfv1 --networkAdmin admin --networkAdminEnrollSecret adminpw --archiveFile food-supply.bna --file networkadmin.card
composer card import --file networkadmin.card
```

你可以通过输入以下内容来验证网络是否已部署：

```
composer network ping --card admin@food-supply
```

你应该看到如下输出：

```
The connection to the network was successfully tested: food-supply
	version: 0.16.0
	participant: org.hyperledger.composer.system.NetworkAdmin#admin

Command succeeded
```

要创建REST API，需要启动`composer-rest-server`并告诉它如何连接到我们的已部署业务网络。现在通过改变目录到`BlockchainPublicRegulationFabric-Food`文件夹来启动服务器，并输入：

```
cd ..
composer-rest-server
```

回答在启动时提出的问题。这些允许composer-rest-server连接到Hyperledger Fabric并配置REST API的生成方式。

- 输入`admin@food-supply`卡片名称。

- 在询问是否在生成的API中使用命名空间时选择`never use namespaces`。

- 当被问及是否需要保护生成的API选择`No`。

- 当被问及是否启用事件发布时选择`Yes`。

- 当询问是否启用TLS安全时选择`No`。

**测试REST API**

如果composer-rest-server启动成功，你应该看到这两行输出：

```
Web server listening at: http://localhost:3000
Browse your REST API at http://localhost:3000/explorer
```

打开Web浏览器并导航到[http://localhost:3000/explorer](http://localhost:3000/explorer)

你应该看到LoopBack API浏览器，允许你查看和测试生成的REST API。按照上述在Composer部分的说明测试业务网络定义，。

## 其他资源

- [Hyperledger Fabric文档](http://hyperledger-fabric.readthedocs.io/en/latest/)

- [Hyperledger Composer文档](https://hyperledger.github.io/composer/introduction/introduction.html)

## License

[Apache 2.0](https://github.com/wbwangk/BlockchainPublicRegulationFabric-Food/blob/master/LICENSE)

