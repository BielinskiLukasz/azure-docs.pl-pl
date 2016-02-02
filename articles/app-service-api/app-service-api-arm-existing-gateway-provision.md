<properties 
    pageTitle="既存のゲートウェイを使用する API アプリのデプロイ" 
    description="Azure リソース マネージャーのテンプレートを使用して、既存のゲートウェイと App Service プランを使用する API アプリをデプロイします。" 
    services="app-service" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="tomfitz"/>


# 既存のゲートウェイを使用する API アプリのプロビジョニング

このトピックでは、Azure API アプリと既存のゲートウェイをデプロイする Azure リソース マネージャーのテンプレートを作成する方法について説明します。 デプロイ対象のリソースを定義する方法を学習し、 
デプロイの実行時に指定するパラメーターを定義する方法。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成方法の詳細については、次を参照してください。 [Azure リソース マネージャー テンプレートの作成](../resource-group-authoring-templates.md)します。

アプリの展開に関する詳細については、次を参照してください。 [予測可能な Azure で複雑なアプリケーション展開](../app-service-web/app-service-deploy-complex-application-predictably.md)します。

完全なテンプレートを参照してください。 [API アプリと既存のゲートウェイ テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-existing/azuredeploy.json)します。

## デプロイ対象

このテンプレートでは、既存の App Service ホスティング プランと既存のゲートウェイに関連付けられている API アプリをデプロイします。

デプロイを自動的に実行するには、次のボタンをクリックします。

[![を Azure にデプロイ](http://azuredeploy.net/deploybutton.png)] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-existing%2Fazuredeploy.json)

## パラメーター

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

既存の App Service ホスティング プランの識別子。

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

既存のホスティング プランの設定。

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## 変数

このテンプレートは、リソースをデプロイするときに使用される変数を定義します。

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }

以降では、値は **variables('packageId')** として使用されています。 API Apps の NuGet パッケージ ID が含まれます。

## デプロイするリソース

### API アプリをホストする Web アプリ

API アプリをホストする Web アプリを作成します。

**kind** が **apiApp** に設定されています。これにより、この Web アプリが API アプリをホストしていることが Azure ポータルに通知されます。 Web アプリは、ポータルの Web アプリの参照ブレードに表示されなくなります。 アプリケーションには、拡張機能が含まれています。 
既定値に空の [API アプリ パッケージをインストールします。 API アプリとホスティング Web アプリの間にリンクが定義されています。 アプリ設定セクションには、API アプリをホストするために必要な値が含まれています。 **serverFarmId** プロパティには、**hostingPlanId** パラメーターで指定した値が設定されます。

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### API アプリ

API アプリを作成します。

ホスティング Web アプリとゲートウェイの名前が API アプリのプロパティとして定義されていることに注意してください。

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json



