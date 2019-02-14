---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: fe0a4c51c4f762d866d572e0cdbd84d9e1c626b7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56247049"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby utworzyć nazwę główną usługi z dostępem do rejestru kontenerów, uruchom następujący skrypt [usługi Azure Cloud Shell](../articles/cloud-shell/overview.md) lub lokalnej instalacji [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Skrypt jest sformatowany do powłoki Bash.

Przed uruchomieniem skryptu, należy zaktualizować `ACR_NAME` zmiennej z nazwą rejestru kontenerów. `SERVICE_PRINCIPAL_NAME` Wartość musi być unikatowa w ramach dzierżawy usługi Azure Active Directory. Jeśli zostanie wyświetlony "`'http://acr-service-principal' already exists.`" błąd, podaj inną nazwę dla jednostki usługi.

Opcjonalnie możesz zmodyfikować `--role` wartości w poleceniu [az ad sp create-for-rbac][/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac], jeśli chcesz udzielić uprawnień różnych. Aby uzyskać pełną listę ról, zobacz [ACR role i uprawnienia](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po uruchomieniu skryptu, zwróć uwagę na nazwy głównej usługi **identyfikator** i **hasło**. Po utworzeniu swoje poświadczenia, można skonfigurować aplikacje i usługi do uwierzytelniania usługi container registry jako nazwy głównej usługi.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Użyj istniejącej nazwy głównej usługi

Aby przyznać dostęp do rejestru do istniejącej jednostki usługi, należy przypisać roli do jednostki usługi. Podobnie jak w przypadku tworzenia nowej usługi jednostki, można przyznać ściągania, wypychania i ściągania i dostęp właściciela, między innymi.

Poniższy skrypt używa polecenia [az przypisania roli create][/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create] udzielenia *ściągnięcia* uprawnienia dla jednostki usługi, możesz określić w `SERVICE_PRINCIPAL_ID`zmiennej. Dostosuj `--role` wartość, jeśli chcesz udzielić inny poziom dostępu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment
