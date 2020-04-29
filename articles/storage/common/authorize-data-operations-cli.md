---
title: Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: Określ sposób autoryzacji operacji na danych w obiektach Blob lub kolejkach przy użyciu interfejsu wiersza polecenia platformy Azure. Operacje na danych można autoryzować przy użyciu poświadczeń usługi Azure AD, klucza dostępu do konta lub z tokenem sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78207695"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autoryzuj dostęp do danych obiektu BLOB lub kolejki za pomocą interfejsu wiersza polecenia platformy Azure

Usługa Azure Storage udostępnia rozszerzenia interfejsu wiersza polecenia platformy Azure, które umożliwiają określenie sposobu autoryzacji operacji na danych obiektu BLOB lub kolejki. Operacje na danych można autoryzować w następujący sposób:

- Za pomocą podmiotu zabezpieczeń Azure Active Directory (Azure AD). Firma Microsoft zaleca korzystanie z poświadczeń usługi Azure AD w celu zapewnienia bezpieczeństwa i łatwość użycia.
- Za pomocą klucza dostępu do konta lub tokenu sygnatury dostępu współdzielonego (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Określ sposób autoryzacji operacji na danych

Polecenie interfejsu wiersza polecenia platformy Azure służące do odczytywania i zapisywania danych `--auth-mode` obiektów blob i kolejek zawierają opcjonalny parametr. Określ ten parametr, aby wskazać, w jaki sposób ma być autoryzowana operacja na danych:

- Ustaw `--auth-mode` parametr na `login` , aby zalogować się przy użyciu podmiotu zabezpieczeń usługi Azure AD (zalecane).
- Ustaw `--auth-mode` parametr na starszą `key` wartość, aby próbować pobrać klucz dostępu konta do użycia na potrzeby autoryzacji. Jeśli pominięto `--auth-mode` parametr, interfejs wiersza polecenia platformy Azure podejmie również próbę pobrania klucza dostępu.

Aby użyć `--auth-mode` parametru, upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom `az --version` , aby sprawdzić zainstalowaną wersję.

> [!IMPORTANT]
> Jeśli pominięto `--auth-mode` parametr lub ustawisz go na `key`, interfejs wiersza polecenia platformy Azure próbuje użyć klucza dostępu do konta w celu autoryzacji. W tym przypadku firma Microsoft zaleca, aby podać klucz dostępu przy użyciu polecenia lub zmiennej środowiskowej **AZURE_STORAGE_KEY** . Aby uzyskać więcej informacji na temat zmiennych środowiskowych, zapoznaj się z sekcją [Ustawianie zmiennych środowiskowych dla parametrów autoryzacji](#set-environment-variables-for-authorization-parameters).
>
> Jeśli nie podasz klucza dostępu, interfejs wiersza polecenia platformy Azure podejmie próbę wywołania dostawcy zasobów usługi Azure Storage w celu pobrania go dla każdej operacji. Wykonanie wielu operacji na danych, które wymagają wywołania dostawcy zasobów może spowodować ograniczenie przepustowości. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [cele skalowalności i wydajności dla dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autoryzuj przy użyciu poświadczeń usługi Azure AD

Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD zwracany jest token dostępu OAuth 2,0. Ten token jest automatycznie używany przez interfejs wiersza polecenia platformy Azure do autoryzowania kolejnych operacji na danych w odniesieniu do magazynu obiektów blob lub Queue. W przypadku obsługiwanych operacji nie jest już konieczne przekazywanie klucza konta ani tokenu SAS przy użyciu polecenia.

Można przypisać uprawnienia do obiektów blob i kolejek danych do podmiotu zabezpieczeń usługi Azure AD za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji na temat ról RBAC w usłudze Azure Storage, zobacz [Zarządzanie prawami dostępu do danych usługi Azure Storage za pomocą RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Uprawnienia do wywoływania operacji na danych

Rozszerzenia usługi Azure Storage są obsługiwane w przypadku operacji na danych obiektów blob i kolejek. Operacje, które można wywołać, zależą od uprawnień udzielonych podmiotowi zabezpieczeń usługi Azure AD, za pomocą którego logujesz się do interfejsu wiersza polecenia platformy Azure. Uprawnienia do kontenerów lub kolejek usługi Azure Storage są przypisywane za pośrednictwem RBAC. Na przykład, jeśli przypisano rolę **czytnika danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują dane z kontenera lub kolejki. Jeśli przypisano rolę **współautor danych obiektów BLOB** , można uruchamiać polecenia skryptów, które odczytują, zapisują lub usuwają kontener lub kolejkę lub zawarte w nich dane.

Aby uzyskać szczegółowe informacje o uprawnieniach wymaganych dla każdej operacji usługi Azure Storage w kontenerze lub kolejce, zobacz [wywoływanie operacji magazynu przy użyciu tokenów OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Przykład: Autoryzuj operację do tworzenia kontenera przy użyciu poświadczeń usługi Azure AD

Poniższy przykład pokazuje, jak utworzyć kontener z interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby utworzyć kontener, należy zalogować się do interfejsu wiersza polecenia platformy Azure i będzie potrzebna Grupa zasobów i konto magazynu. Aby dowiedzieć się, jak utworzyć te zasoby, zobacz [Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../blobs/storage-quickstart-blobs-cli.md).

1. Przed utworzeniem kontenera Przypisz do siebie rolę [współautor danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Mimo że jesteś właścicielem konta, potrzebujesz jawnych uprawnień do wykonywania operacji na danych na koncie magazynu. Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [przyznawanie dostępu do obiektów blob platformy Azure i danych z kolejki RBAC w Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Propagowanie przypisań ról RBAC może potrwać kilka minut.

1. Wywołaj polecenie [AZ Storage Container Create](/cli/azure/storage/container#az-storage-container-create) z `--auth-mode` parametrem ustawionym `login` na, aby utworzyć kontener przy użyciu poświadczeń usługi Azure AD. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autoryzuj przy użyciu klucza dostępu do konta

Jeśli posiadasz klucz konta, możesz wywołać dowolną operację danych usługi Azure Storage. Ogólnie rzecz biorąc, użycie klucza konta jest mniej bezpieczne. W przypadku naruszenia zabezpieczeń klucza konta mogą zostać naruszone wszystkie dane na koncie.

Poniższy przykład pokazuje, jak utworzyć kontener przy użyciu klucza dostępu do konta. Określ klucz konta i podaj `--auth-mode` parametr z `key` wartością:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autoryzuj przy użyciu tokenu SAS

Jeśli masz token SAS, możesz wywołać operacje na danych, które są dozwolone przez sygnaturę dostępu współdzielonego. Poniższy przykład pokazuje, jak utworzyć kontener przy użyciu tokenu sygnatury dostępu współdzielonego:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ustawianie zmiennych środowiskowych dla parametrów autoryzacji

Możesz określić parametry autoryzacji w zmiennych środowiskowych, aby uniknąć uwzględniania ich przy każdym wywołaniu operacji na danych usługi Azure Storage. W poniższej tabeli opisano dostępne zmienne środowiskowe.

| Zmienna środowiskowa                  | Opis                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Nazwa konta magazynu. Ta zmienna powinna być używana w połączeniu z kluczem konta magazynu lub tokenem sygnatury dostępu współdzielonego. Jeśli żaden z nich nie istnieje, interfejs wiersza polecenia platformy Azure próbuje pobrać klucz dostępu konta magazynu za pomocą uwierzytelnionego konta usługi Azure AD. Jeśli wiele poleceń jest wykonywanych w tym samym czasie, może być osiągnięty limit ograniczenia dostawcy zasobów usługi Azure Storage. Aby uzyskać więcej informacji na temat limitów dostawcy zasobów, zobacz [cele skalowalności i wydajności dla dostawcy zasobów usługi Azure Storage](scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Klucz konta magazynu. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Parametry połączenia, które obejmują klucz konta magazynu lub token sygnatury dostępu współdzielonego. Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sygnatury dostępu współdzielonego (SAS). Ta zmienna musi być używana w połączeniu z nazwą konta magazynu.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Tryb autoryzacji, z którym ma zostać uruchomione polecenie. Dozwolone wartości to `login` (zalecane) lub `key`. Jeśli określisz `login`, interfejs wiersza polecenia platformy Azure używa poświadczeń usługi Azure AD do autoryzacji operacji na danych. W przypadku określenia starszego `key` trybu interfejs wiersza polecenia platformy Azure próbuje wykonać zapytanie o klucz dostępu do konta i autoryzować polecenie przy użyciu klucza.    |

## <a name="next-steps"></a>Następne kroki

- [Używanie interfejsu wiersza polecenia platformy Azure do przypisywania roli RBAC na potrzeby dostępu do danych obiektów blob i kolejek](storage-auth-aad-rbac-cli.md)
- [Autoryzuj dostęp do danych obiektów blob i kolejek z tożsamościami zarządzanymi dla zasobów platformy Azure](storage-auth-aad-msi.md)
