---
title: Najlepsze rozwiązania dotyczące szablonów
description: Opisuje zalecane podejścia do tworzenia szablonów Azure Resource Manager. Oferuje sugestie pozwalające uniknąć typowych problemów związanych z korzystaniem z szablonów.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: b87f66f194b9e1daf8fa6a546c02aa4e86caabc3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484248"
---
# <a name="azure-resource-manager-template-best-practices"></a>Najlepsze rozwiązania dotyczące szablonu Azure Resource Manager

W tym artykule przedstawiono zalecenia dotyczące sposobu konstruowania szablonu Menedżer zasobów. Te zalecenia pozwalają uniknąć typowych problemów związanych z wdrażaniem rozwiązania przy użyciu szablonu.

Aby uzyskać zalecenia dotyczące sposobu zarządzania subskrypcjami platformy Azure, zobacz [Azure Enterprise szkielet: prescriptal subscriptions](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Aby zapoznać się z zaleceniami dotyczącymi tworzenia szablonów, które działają we wszystkich środowiskach w chmurze platformy Azure, zobacz [opracowywanie szablonów Azure Resource Manager na potrzeby spójności chmury](templates-cloud-consistency.md).

## <a name="template-limits"></a>Limity szablonów

Ogranicz rozmiar szablonu do 4 MB, a każdy plik parametrów do 64 KB. Limit 4 MB ma zastosowanie do końcowego stanu szablonu po jego rozszerzeniu z powtarzającymi się definicjami zasobów i wartościami zmiennych i parametrów. 

Jest również ograniczona do:

* parametry 256
* 256 zmienne
* 800 zasobów (w tym liczba kopii)
* 64 wartości wyjściowe
* 24 576 znaków w wyrażeniu szablonu

Można przekroczyć niektóre limity szablonów przy użyciu zagnieżdżonego szablonu. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub danych wyjściowych, można połączyć kilka wartości z obiektem. Aby uzyskać więcej informacji, zobacz [obiekty jako parametry](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Grupa zasobów

Podczas wdrażania zasobów w grupie zasobów Grupa zasobów przechowuje metadane dotyczące zasobów. Metadane są przechowywane w lokalizacji grupy zasobów.

Jeśli region grupy zasobów jest tymczasowo niedostępny, nie można zaktualizować zasobów w grupie zasobów, ponieważ metadane są niedostępne. Zasoby w innych regionach będą nadal działać zgodnie z oczekiwaniami, ale nie można ich zaktualizować. Aby zminimalizować ryzyko, Znajdź grupę zasobów i zasoby w tym samym regionie.

## <a name="parameters"></a>Parametry

Informacje przedstawione w tej sekcji mogą być przydatne podczas pracy z [parametrami](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Ogólne zalecenia dotyczące parametrów

* Zminimalizuj użycie parametrów. Zamiast tego należy użyć zmiennych lub wartości literału dla właściwości, które nie muszą być określone podczas wdrażania.

* Użyj notacji CamelCase przypadku nazw parametrów.

* Użyj parametrów dla ustawień, które różnią się w zależności od środowiska, takiego jak jednostka SKU, rozmiar lub pojemność.

* Użyj parametrów nazw zasobów, które chcesz określić do łatwej identyfikacji.

* Podaj opis każdego parametru w metadanych:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Zdefiniuj domyślne wartości parametrów, które nie są poufne. Określając wartość domyślną, łatwiej jest wdrożyć szablon i użytkownicy szablonu widzą przykład odpowiedniej wartości. Każda wartość domyślna parametru musi być prawidłowa dla wszystkich użytkowników w domyślnej konfiguracji wdrożenia. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Aby określić opcjonalny parametr, nie używaj pustego ciągu jako wartości domyślnej. Zamiast tego należy użyć wartości literału lub wyrażenia języka w celu skonstruowania wartości.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Nie należy używać parametru dla wersji interfejsu API dla typu zasobu. Właściwości i wartości zasobów mogą się różnić w zależności od numeru wersji. Funkcja IntelliSense w edytorze kodu nie może określić poprawnego schematu, gdy wersja interfejsu API jest ustawiona na wartość parametru. Zamiast tego należy zwolnić wersję interfejsu API w szablonie.

* Używaj `allowedValues` oszczędnie. Użyj go tylko wtedy, gdy musisz upewnić się, że niektóre wartości nie są uwzględnione w dozwolonych opcjach. Jeśli używasz `allowedValues` zbyt szeroko, możesz zablokować prawidłowe wdrożenia, nie zachowując Aktualności listy.

* Jeśli nazwa parametru w szablonie jest zgodna z parametrem w poleceniu wdrażania programu PowerShell, Menedżer zasobów rozwiązuje ten konflikt nazw przez dodanie przyrostka **FromTemplate** do parametru szablonu. Na przykład jeśli w szablonie zostanie uwzględniony parametr o nazwie **ResourceGroupName** , powoduje to konflikt z parametrem **ResourceGroupName** w poleceniu cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Podczas wdrażania zostanie wyświetlony monit o podanie wartości dla **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Zalecenia dotyczące zabezpieczeń parametrów

* Zawsze używaj parametrów nazw użytkowników i haseł (lub wpisów tajnych).

* Użyj `securestring` dla wszystkich haseł i wpisów tajnych. Jeśli dane poufne są przekazywane w obiekcie JSON, użyj typu `secureObject`. Nie można odczytać parametrów szablonu z bezpiecznym ciągiem lub bezpiecznych typów obiektów po wdrożeniu zasobów. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Nie należy podawać wartości domyślnych dla nazw użytkowników, haseł ani wartości, które wymagają typu `secureString`.

* Nie należy podawać wartości domyślnych dla właściwości, które zwiększają obszar ataków aplikacji.

### <a name="location-recommendations-for-parameters"></a>Zalecenia dotyczące lokalizacji parametrów

* Użyj parametru, aby określić lokalizację zasobów i ustawić wartość domyślną `resourceGroup().location`. Podanie parametru Location umożliwia użytkownikom szablonu określenie lokalizacji, do której mają uprawnienia do wdrożenia.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Nie określaj `allowedValues` parametru Location. Określone lokalizacje mogą nie być dostępne we wszystkich chmurach.

* Użyj wartości parametru Location dla zasobów, które mogą znajdować się w tej samej lokalizacji. To podejście minimalizuje liczbę użytkowników, którzy otrzymają prośbę o podanie informacji o lokalizacji.

* W przypadku zasobów, które nie są dostępne we wszystkich lokalizacjach, należy użyć oddzielnego parametru lub określić wartość lokalizacji literału.

## <a name="variables"></a>Zmienne

Poniższe informacje mogą być przydatne podczas pracy ze [zmiennymi](template-variables.md):

* Użyj notacji CamelCase przypadku nazw zmiennych.

* Użyj zmiennych dla wartości, które mają być używane więcej niż raz w szablonie. Jeśli wartość jest używana tylko raz, ustalona wartość ułatwia odczytywanie szablonu.

* Używaj zmiennych dla wartości, które są konstruowane na podstawie złożonego rozmieszczenia funkcji szablonu. Szablon jest łatwiejszy do odczytania, gdy złożone wyrażenie pojawia się tylko w zmiennych.

* Nie używaj zmiennych dla `apiVersion` w zasobie. Wersja interfejsu API określa schemat zasobu. Często nie można zmienić wersji bez zmiany właściwości zasobu.

* Nie można użyć funkcji [Reference](template-functions-resource.md#reference) w sekcji **zmiennych** szablonu. Funkcja **Reference** dziedziczy jej wartość ze stanu środowiska uruchomieniowego zasobu. Jednak zmienne są rozwiązywane podczas początkowej analizy szablonu. Konstruowanie wartości, które wymagają funkcji **referencyjnej** , bezpośrednio w sekcji **zasoby** lub dane **wyjściowe** szablonu.

* Dołącz zmienne nazw zasobów, które muszą być unikatowe.

* Użyj [pętli kopiowania w zmiennych](create-multiple-instances.md#variable-iteration) , aby utworzyć powtarzający się wzorzec obiektów JSON.

* Usuń nieużywane zmienne.

## <a name="resource-dependencies"></a>Zależności zasobów

Podczas wybierania [zależności](define-resource-dependency.md) , które należy ustawić, należy przestrzegać następujących wytycznych:

* Użyj funkcji **Reference** i przekaż nazwę zasobu, aby ustawić niejawną zależność między zasobami, które muszą udostępniać właściwość. Nie dodawaj jawnego elementu `dependsOn`, gdy została już zdefiniowana zależność niejawna. Takie podejście zmniejsza ryzyko związane z niepotrzebnymi zależnościami.

* Ustaw zasób podrzędny jako zależny od jego zasobu nadrzędnego.

* Zasoby z [elementem Condition](conditional-resource-deployment.md) ustawionym na wartość false są automatycznie usuwane z kolejności zależności. Ustaw zależności, tak jakby zasób jest zawsze wdrażany.

* Zezwól na kaskadowe zależności bez ustawiania ich jawnie. Na przykład maszyna wirtualna zależy od interfejsu sieci wirtualnej, a interfejs sieci wirtualnej zależy od sieci wirtualnej i publicznych adresów IP. W związku z tym, maszyna wirtualna jest wdrażana po wszystkich trzech zasobach, ale nie ustawia jawnie maszyny wirtualnej jako zależnej od wszystkich trzech zasobów. Takie podejście wyjaśnia kolejność zależności i ułatwia późniejsze zmiany szablonu.

* Jeśli wartość można ustalić przed wdrożeniem, spróbuj wdrożyć zasób bez zależności. Na przykład jeśli wartość konfiguracji wymaga nazwy innego zasobu, może to oznaczać, że zależność nie jest wymagana. Te wskazówki nie zawsze działają, ponieważ niektóre zasoby weryfikują istnienie innego zasobu. Jeśli wystąpi błąd, Dodaj zależność.

## <a name="resources"></a>Zasoby

Poniższe informacje mogą być przydatne podczas pracy z [zasobami](template-syntax.md#resources):

* Aby pomóc innym współautorom zrozumieć cel zasobu, określ **Komentarze** dla każdego zasobu w szablonie:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Jeśli używasz *publicznego punktu końcowego* w szablonie (na przykład publicznego punktu końcowego usługi Azure Blob Storage), *nie używaj twardej* nazwy przestrzeni nazw. Użyj funkcji **Reference** , aby dynamicznie pobrać przestrzeń nazw. Tego podejścia można użyć do wdrożenia szablonu w różnych publicznych środowiskach nazw bez ręcznej zmiany punktu końcowego w szablonie. Ustaw wersję interfejsu API na tę samą wersję, która jest używana dla konta magazynu w szablonie:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Jeśli konto magazynu zostanie wdrożone w tym samym szablonie, który tworzysz, a nazwa konta magazynu nie jest współdzielona z innym zasobem w szablonie, nie trzeba określać przestrzeni nazw dostawcy ani apiVersion podczas odwoływania się do zasobu. W poniższym przykładzie przedstawiono uproszczoną składnię:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Można również odwołać się do istniejącego konta magazynu, które znajduje się w innej grupie zasobów:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Przypisz publiczne adresy IP do maszyny wirtualnej tylko wtedy, gdy jest ona wymagana przez aplikację. Aby nawiązać połączenie z maszyną wirtualną (VM) na potrzeby debugowania lub w celach zarządzania lub administracyjne, Użyj reguł NAT dla ruchu przychodzącego, bramy sieci wirtualnej lub serwera przesiadkowego.
   
     Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi, zobacz:
   
   * [Uruchamianie maszyn wirtualnych dla architektury N-warstwowej na platformie Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurowanie dostępu do usługi WinRM dla maszyn wirtualnych w Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Zezwalaj na dostęp zewnętrzny do maszyny wirtualnej przy użyciu Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej przy użyciu programu PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Zezwalanie na dostęp zewnętrzny do maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Właściwość **wartość domainnamelabel** dla publicznych adresów IP musi być unikatowa. Wartość **wartość domainnamelabel** musi mieć długość od 3 do 63 znaków i postępować zgodnie z regułami określonymi w tym wyrażeniu regularnym: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Ponieważ funkcja **uniqueString** generuje ciąg o długości 13 znaków, parametr **dnsPrefixString** jest ograniczony do 50 znaków:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Po dodaniu hasła do niestandardowego rozszerzenia skryptu Użyj właściwości **sekcji commandtoexecute** we właściwości **protectedSettings** :
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Aby upewnić się, że wpisy tajne są szyfrowane, gdy są one przesyłane jako parametry do maszyn wirtualnych i rozszerzeń, użyj właściwości **protectedSettings** odpowiednich rozszerzeń.
   > 
   > 

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat struktury pliku szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](template-syntax.md).
* Aby zapoznać się z zaleceniami dotyczącymi tworzenia szablonów, które działają we wszystkich środowiskach w chmurze platformy Azure, zobacz [opracowywanie szablonów Azure Resource Manager na potrzeby spójności chmury](templates-cloud-consistency.md).
