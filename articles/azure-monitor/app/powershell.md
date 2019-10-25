---
title: Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: Automatyzacja tworzenia zasobów, alertów i testów dostępności w programie PowerShell oraz zarządzania nimi przy użyciu szablonu Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 938511069500c551eb526b6c7238546b85d59dce
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818929"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Zarządzanie zasobami Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule przedstawiono sposób automatycznego automatyzowania tworzenia i aktualizowania zasobów [Application Insights](../../azure-monitor/app/app-insights-overview.md) przy użyciu usługi Azure Resource Management. Można to zrobić na przykład w ramach procesu kompilacji. Wraz z zasobem podstawowa Application Insights można tworzyć [testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md), konfigurować [alerty](../../azure-monitor/app/alerts.md), ustawiać [schemat cenowy](pricing.md)oraz tworzyć inne zasoby platformy Azure.

Kluczem do tworzenia tych zasobów są szablony JSON dla [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). Podstawowa procedura: pobieranie definicji JSON istniejących zasobów; Sparametryzuj pewne wartości, takie jak Names; a następnie uruchom szablon, gdy chcesz utworzyć nowy zasób. Można spakować wiele zasobów razem, aby utworzyć je wszystkie w jednym miejscu, na przykład monitor aplikacji z testami dostępności, alertami i magazynem na potrzeby eksportu ciągłego. Istnieje kilka subtleties niektórych parameterizations, które wyjaśnimy tutaj.

## <a name="one-time-setup"></a>Konfiguracja jednorazowa
Jeśli nie korzystasz z programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł Azure PowerShell na komputerze, na którym chcesz uruchomić skrypty:

1. Zainstaluj [Instalator platformy Microsoft Web (w wersji 5 lub nowszej)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Służy do instalowania programu Microsoft Azure PowerShell.

Oprócz używania szablonów Menedżer zasobów istnieje bogaty zestaw [Application Insights poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), które ułatwiają konfigurowanie Application Insights zasobów programowo. Funkcje dostępne w poleceniach cmdlet obejmują:

* Tworzenie i usuwanie zasobów Application Insights
* Pobierz listy zasobów Application Insights i ich właściwości
* Tworzenie eksportu ciągłego i zarządzanie nim
* Tworzenie kluczy aplikacji i zarządzanie nimi
* Ustaw limit dzienny
* Ustawianie planu cenowego

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Tworzenie zasobów Application Insights przy użyciu polecenia cmdlet programu PowerShell

Poniżej przedstawiono sposób tworzenia nowego zasobu usługi Application Insights w centrum danych we/wschodnich stanach USA przy użyciu polecenia cmdlet [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Tworzenie Application Insights zasobów przy użyciu szablonu Menedżer zasobów

Poniżej przedstawiono sposób tworzenia nowego zasobu Application Insights przy użyciu szablonu Menedżer zasobów.

### <a name="create-the-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager

Utwórz nowy plik JSON — Wywołaj go `template1.json` w tym przykładzie. Skopiuj do niej tę zawartość:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Tworzenie nowego zasobu Application Insights przy użyciu szablonu Menedżer zasobów

1. W programie PowerShell Zaloguj się do platformy Azure przy użyciu `$Connect-AzAccount`
2. Ustaw kontekst na subskrypcję z `Set-AzContext "<subscription ID>"`
2. Uruchom nowe wdrożenie, aby utworzyć nowy zasób Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` jest grupą, w której chcesz utworzyć nowe zasoby.
   * `-TemplateFile` musi następować przed parametrami niestandardowymi.
   * `-appName` nazwę zasobu do utworzenia.

Możesz dodać inne parametry — opisy można znaleźć w sekcji Parametry szablonu.

## <a name="get-the-instrumentation-key"></a>Pobieranie klucza Instrumentacji

Po utworzeniu zasobu aplikacji należy użyć klucza Instrumentacji: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Aby wyświetlić listę wielu innych właściwości zasobu Application Insights, użyj:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Dodatkowe właściwości są dostępne za pośrednictwem poleceń cmdlet:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Zapoznaj się z [dokumentacją](https://docs.microsoft.com/powershell/module/az.applicationinsights) dotyczącą parametrów tych poleceń cmdlet.  

## <a name="set-the-data-retention"></a>Ustaw przechowywanie danych 

Aby uzyskać bieżące przechowywanie danych dla zasobu Application Insights, można użyć narzędzia OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Dowiedz się więcej na temat ARMClient z artykułów przez [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)).  Oto przykład użycia `ARMClient`, aby uzyskać bieżące przechowywanie:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Aby ustawić przechowywanie, polecenie jest podobne do UMIESZCZAnia:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Aby ustawić przechowywanie danych na 365 dni przy użyciu szablonu powyżej, uruchom polecenie:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -retentionInDays 365 `
               -appName myApp
```

Aby zmienić przechowywanie, można również użyć następującego skryptu. Skopiuj ten skrypt, aby zapisać jako `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ten skrypt może być następnie używany jako:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Ustaw limit dzienny

Aby uzyskać właściwości dziennego zakończenia, użyj polecenia cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić właściwości dziennego limitu, użyj tego samego polecenia cmdlet. Na przykład, aby ustawić limit na 300 GB/dzień, 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Ustawianie planu cenowego 

Aby uzyskać bieżący plan cenowy, należy użyć polecenia cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić plan cenowy, użyj tego samego polecenia cmdlet z określonym `-PricingPlan`:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Możesz również ustawić plan cenowy dla istniejącego zasobu Application Insights przy użyciu szablonu Menedżer zasobów powyżej, pomijając zasób "Microsoft. Insights/Components" i węzeł `dependsOn` z zasobu rozliczeń. Na przykład aby ustawić dla planu na GB (dawniej nazywany planem Basic), uruchom polecenie:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|zamierza|
|---|---|
|1|Za GB (dawniej nazwany plan podstawowy)|
|2|Na węzeł (dawniej Nazwa planu przedsiębiorstwa)|

## <a name="add-a-metric-alert"></a>Dodawanie alertu metryki

Aby skonfigurować alert metryki w tym samym czasie co zasób aplikacji, należy scalić kod podobny do tego w pliku szablonu:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Po wywołaniu szablonu można opcjonalnie dodać ten parametr:

    `-responseTime 2`

Sparametryzuj inne pola. 

Aby sprawdzić nazwy typów i szczegóły konfiguracji innych reguł alertów, Utwórz regułę ręcznie, a następnie sprawdź ją w [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Dodaj Test dostępności

Ten przykład dotyczy testu ping (w celu przetestowania pojedynczej strony).  

W teście dostępności **znajdują się dwie części** : test i alert informujący o błędach.

Scal następujący kod w pliku szablonu, który tworzy aplikację.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Aby odnaleźć kody dla innych lokalizacji testowych lub zautomatyzować tworzenie bardziej złożonych testów sieci Web, Utwórz przykład ręcznie, a następnie Sparametryzuj kod z [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Dodaj więcej zasobów

Aby zautomatyzować tworzenie dowolnego innego zasobu dowolnego rodzaju, Utwórz przykład ręcznie, a następnie skopiuj i Sparametryzuj jego kod z [Azure Resource Manager](https://resources.azure.com/). 

1. Otwórz [Azure Resource Manager](https://resources.azure.com/). Przejdź w dół `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` do zasobu aplikacji. 
   
    ![Nawigacja w Azure Resource Explorer](./media/powershell/01.png)
   
    *Składniki* to podstawowe zasoby Application Insights do wyświetlania aplikacji. Istnieją oddzielne zasoby dla skojarzonych reguł alertów i testów dostępności sieci Web.
2. Skopiuj kod JSON składnika do odpowiedniego miejsca w `template1.json`.
3. Usuń następujące właściwości:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otwórz sekcje `webtests` i `alertrules` i skopiuj dane JSON dla poszczególnych elementów do szablonu. (Nie Kopiuj z węzłów `webtests` lub `alertrules`: Przejdź do elementów znajdujących się poniżej).
   
    Każdy test sieci Web ma skojarzoną regułę alertu, dlatego należy skopiować oba te testy.
   
    Możesz również uwzględnić alerty dotyczące metryk. [Nazwy metryk](powershell-alerts.md#metric-names).
5. Wstaw ten wiersz do każdego zasobu:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Sparametryzuj szablonu
Teraz należy zamienić określone nazwy na parametry. Aby [Sparametryzuj szablon](../../azure-resource-manager/resource-group-authoring-templates.md), należy napisać wyrażenia przy użyciu [zestawu funkcji pomocnika](../../azure-resource-manager/resource-group-template-functions.md). 

Nie można Sparametryzuj tylko części ciągu, więc Użyj `concat()` do kompilowania ciągów.

Poniżej przedstawiono przykłady podstawień, które należy wykonać. Istnieje kilka wystąpień każdego podstawiania. Mogą być potrzebne inne osoby w szablonie. W tych przykładach użyto parametrów i zmiennych zdefiniowanych w górnej części szablonu.

| find | Zamień na |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (małe litery) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Ustawianie zależności między zasobami
Platforma Azure powinna skonfigurować zasoby w ścisłej kolejności. Aby upewnić się, że jedna konfiguracja zostanie zakończona przed rozpoczęciem następnego, Dodaj linie zależności:

* W zasobie Test dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* W zasobie alertu dla testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Następne kroki
Inne artykuły dotyczące usługi Automation:

* Utwórz szybką metodę [Application Insights zasobów](powershell-script-create-resource.md) bez użycia szablonu.
* [Konfigurowanie alertów](powershell-alerts.md)
* [Tworzenie testów sieci Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](powershell-azure-diagnostics.md)
* [Wdrażanie na platformie Azure z usługi GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Utwórz adnotacje wydania](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)