---
title: Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight
description: Dowiedz się, jak używać Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: hrasheed
ms.openlocfilehash: a8ef347b874bf01fc2832d0a785f6fcec58ef095
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961224"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight

Azure Data Lake Storage Gen2 to usługa magazynu w chmurze przeznaczona dla analizy danych Big Data, oparta na usłudze Azure Blob Storage. Data Lake Storage Gen2 łączy możliwości usługi Azure Blob Storage i Azure Data Lake Storage Gen1. Usługa będąca wynikiem oferty oferuje funkcje z Azure Data Lake Storage Gen1, takie jak semantyka systemu plików, zabezpieczenia na poziomie katalogu i na poziomie plików oraz skalowalność, a także niski koszt magazynu warstwowego, wysokiej dostępności i odzyskiwania po awarii. z usługi Azure Blob Storage.

## <a name="data-lake-storage-gen2-availability"></a>Dostępność Data Lake Storage Gen2

Data Lake Storage Gen2 jest dostępna jako opcja magazynu dla niemal wszystkich typów klastrów usługi Azure HDInsight zarówno jako domyślnego, jak i dodatkowego konta magazynu. HBase jednak może mieć tylko jedno konto Data Lake Storage Gen2.

Aby zapoznać się z pełnym porównaniem opcji tworzenia klastra przy użyciu Data Lake Storage Gen2, zobacz [porównanie opcji magazynu do użycia z klastrami usługi Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Po wybraniu Data Lake Storage Gen2 jako **podstawowego typu magazynu**nie można wybrać konta Data Lake Storage Gen1 jako dodatkowego magazynu.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Tworzenie klastra przy użyciu Data Lake Storage Gen2 za pomocą Azure Portal

Aby utworzyć klaster usługi HDInsight, który używa Data Lake Storage Gen2 dla magazynu, wykonaj następujące kroki, aby skonfigurować konto Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz przypisane przez **użytkownika** , a następnie kliknij pozycję **tożsamość zarządzana przypisana przez użytkownika**.
1. Kliknij przycisk **Utwórz**.
1. Wprowadź nazwę tożsamości zarządzanej, wybierz odpowiednią subskrypcję, grupę zasobów i lokalizację.
1. Kliknij przycisk **Utwórz**.

Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](hdinsight-managed-identities.md).

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Utwórz konto Data Lake Storage Gen2

Utwórz konto usługi Azure Data Lake Storage Gen2. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W lewym górnym rogu kliknij pozycję **Utwórz zasób**.
1. W polu wyszukiwania wpisz **Storage** i kliknij pozycję **konto magazynu**.
1. Kliknij przycisk **Utwórz**.
1. Na ekranie **Tworzenie konta magazynu** :
    1. Wybierz prawidłową subskrypcję i grupę zasobów.
    1. Wprowadź nazwę konta Data Lake Storage Gen2. Aby uzyskać więcej informacji na temat konwencji nazewnictwa kont magazynu, zobacz [konwencje nazewnictwa dla zasobów platformy Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#storage).
    1. Kliknij kartę **Zaawansowane** .
    1. Kliknij pozycję **włączone** obok **hierarchicznej przestrzeni nazw** w obszarze **Data Lake Storage Gen2**.
    1. Kliknij pozycję **Przegląd + utwórz**.
    1. Kliknij przycisk **Utwórz**.

Aby uzyskać więcej informacji na temat innych opcji podczas tworzenia konta magazynu [, zobacz Szybki Start: Utwórz konto](../storage/blobs/data-lake-storage-quickstart-create-account.md)magazynu Azure Data Lake Storage Gen2.

![Zrzut ekranu przedstawiający tworzenie konta magazynu w Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Skonfiguruj uprawnienia dla tożsamości zarządzanej na koncie Data Lake Storage Gen2

Przypisz zarządzaną tożsamość do roli **właściciela danych obiektów blob magazynu** na koncie magazynu.

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.
1. Wybierz konto magazynu, a następnie wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
    
    ![Zrzut ekranu przedstawiający ustawienia kontroli dostępu do magazynu](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)
    
1. Wybierz przycisk **+ Dodaj przypisanie roli** , aby dodać nową rolę.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **właściciela danych obiektu blob magazynu** . Następnie wybierz subskrypcję, która ma zarządzaną tożsamość i konto magazynu. Następnie wyszukaj, aby zlokalizować utworzoną wcześniej tożsamość zarządzaną przypisaną przez użytkownika. Na koniec Wybierz zarządzaną tożsamość i zostanie ona wyświetlona w obszarze **wybranych członków**.
    
    ![Zrzut ekranu przedstawiający sposób przypisywania roli RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)
    
1. Wybierz pozycję **Zapisz**. Wybrana tożsamość przypisana przez użytkownika jest teraz wyświetlana w obszarze wybranej roli.
1. Po zakończeniu początkowej konfiguracji można utworzyć klaster za pomocą portalu. Klaster musi znajdować się w tym samym regionie świadczenia usługi Azure co konto magazynu. W sekcji **Magazyn** w menu Tworzenie klastra wybierz następujące opcje:
        
    * W obszarze **Typ magazynu podstawowego**wybierz pozycję **Azure Data Lake Storage Gen2**.
    * W obszarze **Wybierz konto magazynu**Wyszukaj i wybierz nowo utworzone konto magazynu Data Lake Storage Gen2.
        
        ![Ustawienia magazynu na potrzeby używania Data Lake Storage Gen2 z usługą Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * W obszarze **tożsamość**Wybierz poprawną subskrypcję i nowo utworzoną tożsamość zarządzaną przez użytkownika.
        
        ![Ustawienia tożsamości na potrzeby używania Data Lake Storage Gen2 z usługą Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> Aby dodać konto Data Lake Storage Gen2 pomocniczego, na poziomie konta magazynu wystarczy przypisać zarządzaną tożsamość utworzoną wcześniej do nowego konta magazynu Data Lake Storage Gen2, które chcesz dodać. Upewnij się, że Dodawanie konta Data Lake Storage Gen2 pomocniczego za pośrednictwem bloku "dodatkowe konta magazynu" w usłudze HDInsight nie jest obsługiwane. 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Tworzenie klastra przy użyciu Data Lake Storage Gen2 za pomocą interfejsu wiersza polecenia platformy Azure

Możesz [pobrać przykładowy plik szablonu](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) i [pobrać przykładowy plik parametrów](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Przed użyciem szablonu i poniższego fragmentu kodu interfejsu wiersza polecenia platformy Azure Zastąp następujące symbole zastępcze odpowiednimi wartościami:

| Symbol zastępczy | Opis |
|---|---|
| `<SUBSCRIPTION_ID>` | Identyfikator subskrypcji platformy Azure |
| `<RESOURCEGROUPNAME>` | Grupa zasobów, w której ma zostać utworzony nowy klaster i konto magazynu. |
| `<MANAGEDIDENTITYNAME>` | Nazwa tożsamości zarządzanej, która będzie mieć uprawnienia na koncie Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nowe konto Azure Data Lake Storage Gen2, które zostanie utworzone. |
| `<CLUSTERNAME>` | Nazwa klastra usługi HDInsight. |
| `<PASSWORD>` | Wybrane hasło do logowania się do klastra przy użyciu protokołu SSH oraz pulpitu nawigacyjnego Ambari. |

Poniższy fragment kodu wykonuje następujące czynności wstępne:

1. Loguje się do konta platformy Azure.
1. Ustawia aktywną subskrypcję, w której będą wykonywane operacje tworzenia.
1. Tworzy nową grupę zasobów dla nowych działań wdrożenia. 
1. Tworzy tożsamość zarządzaną przypisaną przez użytkownika.
1. Dodaje rozszerzenie interfejsu wiersza polecenia platformy Azure w celu używania funkcji dla Data Lake Storage Gen2.
1. Tworzy nowe konto Data Lake Storage Gen2 przy użyciu `--hierarchical-namespace true` flagi. 

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Następnie zaloguj się do portalu. Dodaj nową tożsamość zarządzaną przez użytkownika do roli **współautor danych obiektów blob magazynu** na koncie magazynu, zgodnie z opisem w kroku 3 w sekcji [Używanie Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Po przypisaniu roli do tożsamości zarządzanej przypisanej przez użytkownika należy wdrożyć szablon przy użyciu poniższego fragmentu kodu.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Kontrola dostępu do Data Lake Storage Gen2 w usłudze HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jakie rodzaje uprawnień Data Lake Storage Gen2 obsługiwać?

Data Lake Storage Gen2 używa modelu kontroli dostępu, który obsługuje zarówno kontrolę dostępu opartą na rolach (RBAC), jak i listę kontroli dostępu (ACL), takich jak. Data Lake Storage Gen1 obsługuje listy kontroli dostępu tylko w celu kontrolowania dostępu do danych.

Funkcja RBAC korzysta z przypisań ról w celu efektywnego stosowania zestawów uprawnień dla użytkowników, grup i jednostek usługi dla zasobów platformy Azure. Zazwyczaj te zasoby platformy Azure są ograniczone do zasobów najwyższego poziomu (na przykład kont usługi Azure Storage). W przypadku usługi Azure Storage, a także Data Lake Storage Gen2, ten mechanizm został rozszerzony do zasobu systemu plików.

 Aby uzyskać więcej informacji na temat uprawnień do plików za pomocą RBAC, zobacz [Kontrola dostępu oparta na rolach (RBAC) na platformie Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Aby uzyskać więcej informacji o uprawnieniach plików z listami ACL, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak mogę kontrolować dostęp do danych w Data Lake Storage Gen2?

Możliwość dostępu do plików w Data Lake Storage Gen2 przez klaster usługi HDInsight jest kontrolowana przez zarządzane tożsamości. Tożsamość zarządzana to tożsamość zarejestrowana w Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Przy użyciu tożsamości zarządzanych nie trzeba rejestrować nazw głównych usług w usłudze Azure AD ani zachować poświadczeń, takich jak certyfikaty.

Usługi platformy Azure mają dwa typy zarządzanych tożsamości: przypisane do systemu i przypisane przez użytkownika. Usługa HDInsight używa zarządzanych tożsamości przypisanych przez użytkownika w celu uzyskania dostępu do Data Lake Storage Gen2. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure. W ramach procesu tworzenia platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, której ufa używana subskrypcja. Utworzoną tożsamość można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

Cykl życiowy tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życiowego wystąpień usługi platformy Azure, do których została przypisana. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [jak działają zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak mogę ustawić uprawnienia dla użytkowników usługi Azure AD do wykonywania zapytań dotyczących danych w Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług?

Aby ustawić uprawnienia dla użytkowników do wykonywania zapytań dotyczących danych, należy użyć grup zabezpieczeń usługi Azure AD jako przypisanego podmiotu zabezpieczeń na listach ACL. Nie należy bezpośrednio przypisywać uprawnień dostępu do plików do poszczególnych użytkowników lub podmiotów usługi. W przypadku korzystania z grup zabezpieczeń usługi Azure AD w celu kontrolowania przepływu uprawnień można dodawać i usuwać użytkowników lub jednostki usługi bez ponownego zastosowania list kontroli dostępu do całej struktury katalogów. Wystarczy dodać lub usunąć użytkowników z odpowiedniej grupy zabezpieczeń usługi Azure AD. Listy ACL nie są dziedziczone, dlatego ponowne zastosowanie list kontroli dostępu wymaga aktualizacji listy ACL dla każdego pliku i podkatalogu.

## <a name="next-steps"></a>Następne kroki

* [Integracja z usługą Azure HDInsight z usługą Data Lake Storage Gen2 Preview — lista ACL i aktualizacja zabezpieczeń](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Wprowadzenie do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
