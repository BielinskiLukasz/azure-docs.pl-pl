### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
**Połączonej usługi Azure Storage** umożliwia połączenie konta usługi Azure storage do usługi Azure data factory przy użyciu **klucz konta**, co umożliwia usługi data factory z globalnego dostępu do usługi Azure Storage. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla połączonej usługi Azure Storage.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **AzureStorage** |Yes |
| Parametry połączenia |Określ informacje potrzebne do łączenia z usługą Azure storage dla właściwości connectionString. |Yes |

Zobacz następujący artykuł, wykonując czynności przedstawione widok/kopiowania klucza konta usługi Azure Storage: [wyświetlanie, kopiowanie i ponowne generowanie magazynu, klucze dostępu](../articles/storage/common/storage-create-storage-account.md#manage-your-storage-account).

**Przykład:**  

```json
{  
    "name": "StorageLinkedService",  
    "properties": {  
        "type": "AzureStorage",  
        "typeProperties": {  
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
        }  
    }  
}  
```

### <a name="azure-storage-sas-linked-service"></a>Usługę połączoną usługi Azure Storage sygnatury dostępu współdzielonego
Sygnatury dostępu współdzielonego (SAS) zapewnia delegowany dostęp do zasobów na koncie magazynu. Umożliwia on przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu w określonym przedziale czasu i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do Twojego konta. Sygnatura dostępu Współdzielonego to identyfikator URI, który obejmuje jego parametry zapytań, wszystkie informacje niezbędne do uwierzytelnionego dostępu do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu przy użyciu sygnatury dostępu Współdzielonego, klient musi tylko przekazanej sygnatury dostępu Współdzielonego do odpowiedniego konstruktora lub metody. Aby uzyskać szczegółowe informacje na temat sygnatury dostępu Współdzielonego, zobacz [sygnatur dostępu współdzielonego: opis modelu sygnatur dostępu Współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Usługa Azure Data Factory obsługuje teraz tylko **sygnatury dostępu Współdzielonego usługi** , ale nie sygnatury dostępu Współdzielonego konta. Zobacz [typów z sygnatury dostępu współdzielonego](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) szczegółowe informacje na temat tych dwóch typów oraz sposobu konstruowania. Zanotuj adres URL sygnatury dostępu Współdzielonego generable z witryny Azure portal lub Eksplorator usługi Storage to konto sygnatury dostępu Współdzielonego, który nie jest obsługiwany.

> [!TIP]
> Można wykonać poniższych poleceń programu PowerShell, aby wygenerować sygnaturę dostępu Współdzielonego usługi dla konta usługi storage (Zastąp zastępcze i Udziel uprawnienia potrzebne): `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Usługa połączona sygnatury dostępu Współdzielonego Azure Storage pozwala połączyć konto usługi Azure Storage do usługi Azure data factory przy użyciu sygnatury dostępu współdzielonego (SAS). Usługi data factory zapewnia ograniczony/czasowo dostęp do wszystkich/dla zasobów (obiekt blob/kontenera) w magazynie. Poniższa tabela zawiera opis dla elementów JSON, które są specyficzne dla usługi połączonej usługi Azure Storage sygnatury dostępu Współdzielonego. 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **AzureStorageSas** |Yes |
| sasUri |Określ udostępniony URI podpisu dostępu do zasobów usługi Azure Storage, np. obiektów blob, kontenerów lub tabeli.  |Yes |

**Przykład:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"   
        }  
    }  
}  
```

Podczas tworzenia **identyfikatora URI połączenia SAS**, biorąc pod uwagę następujące czynności:  

* Ustaw odpowiednie odczytu/zapisu **uprawnienia** na obiekty w zależności od sposobu połączonej usługi (Odczyt, zapis, Odczyt/zapis) jest używany w fabryce danych.
* Ustaw **czas wygaśnięcia** odpowiednio. Upewnij się, że dostęp do obiektów usługi Azure Storage nie wygaśnie w aktywnym okresie potoku.
* Identyfikator URI powinny być tworzone na poziomie tabeli oparte na potrzeby lub kontener prawym/obiekt blob. Identyfikator Uri sygnatury dostępu Współdzielonego obiektu blob platformy Azure umożliwia usłudze Data Factory dostęp do tego konkretnego obiektu blob. Identyfikator Uri sygnatury dostępu Współdzielonego do kontenera obiektów blob platformy Azure umożliwia usłudze Data Factory do iteracji przez obiekty BLOB w kontenerze. Jeśli trzeba zapewnić dostęp później obiektów innych/mniejszej lub zaktualizować identyfikatora URI sygnatury dostępu Współdzielonego, pamiętaj, aby zaktualizować połączoną usługę o nowy identyfikator URI.   

