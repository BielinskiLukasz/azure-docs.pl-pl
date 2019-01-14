---
title: Kopiowanie danych z systemu plików HDFS przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kopiować dane ze źródła chmury lub środowisku lokalnym systemem plików HDFS do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: a5df9d4d323158ee52c872b0122fdd28d9f74979
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019864"
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiowanie danych z systemu plików HDFS przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, z której korzystasz:"]
> * [Wersja 1](v1/data-factory-hdfs-connector.md)
> * [Bieżąca wersja](connector-hdfs.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z systemu plików HDFS. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z systemu plików HDFS, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych jako źródła/ujścia działania kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik systemu plików HDFS obsługuje:

- Kopiowanie plików przy użyciu **Windows** (Kerberos) lub **anonimowe** uwierzytelniania.
- Kopiowanie plików przy użyciu **webhdfs** protokołu lub **wbudowane narzędzia DistCp** pomocy technicznej.
- Kopiowanie plików jako — jest lub analizowania/Generowanie plików za pomocą [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z systemu plików HDFS, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](concepts-integration-runtime.md) artykuł, aby poznać szczegóły.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonego w systemie plików hdfs.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku systemu plików HDFS połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **System plików Hdfs**. | Yes |
| url |Adres URL do systemu plików HDFS |Yes |
| Element authenticationType | Dozwolone wartości to: **Anonimowe**, lub **Windows**. <br><br> Aby użyć **uwierzytelnianie Kerberos** łącznika systemu plików HDFS można znaleźć w [w tej sekcji](#use-kerberos-authentication-for-hdfs-connector) odpowiednio skonfigurować swoje środowisko lokalne. |Yes |
| userName |Uwierzytelnianie nazwy użytkownika dla Windows. Dla uwierzytelniania Kerberos, określ `<username>@<domain>.com`. |Tak (dla uwierzytelniania Windows) |
| hasło |Hasło do uwierzytelniania Windows. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (dla uwierzytelniania Windows) |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

**Przykład: użycie uwierzytelniania anonimowego**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przy użyciu uwierzytelniania Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych systemu plików HDFS.

Aby skopiować dane z systemu plików HDFS, należy ustawić właściwość typu zestawu danych na **FileShare**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **Udział plików** |Yes |
| folderPath | Ścieżka do folderu. Filtr z symbolami wieloznacznymi nie jest obsługiwana. Na przykład: folder/podfolder / |Yes |
| fileName |  **Filtr nazwy lub symbol wieloznaczny** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub jeden znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"`<br/>Użyj `^` jako znak ucieczki, jeśli Twoje rzeczywiste nazwy plików symboli wieloznacznych lub ten znak ucieczki wewnątrz. |Nie |
| format | Jeśli chcesz **skopiuj pliki — jest** między opartych na plikach magazynów (kopia binarna), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w obszarze format ma jedną z następujących wartości. Aby uzyskać więcej informacji, zobacz [Format tekstu](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [formatu Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Brak (tylko w przypadku scenariusza kopia binarna) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Są obsługiwane poziomy: **Optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o określonej nazwie, należy określić **folderPath** z część z folderem i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw plików w folderze, podaj **folderPath** z część z folderem i **fileName** z filtr z symbolami wieloznacznymi.

**Przykład:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez system plików HDFS źródło.

### <a name="hdfs-as-source"></a>System plików HDFS jako źródło

Aby skopiować dane z systemu plików HDFS, należy ustawić typ źródłowego w działaniu kopiowania, aby **HdfsSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **HdfsSource** |Yes |
| cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z folderów podrzędnych lub tylko z określonego folderu. Należy pamiętać podczas cyklicznego jest ustawiona na wartość PRAWDA, a obiekt sink jest magazynu opartego na pliku, pusty folder/podrzędnych — folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |
| distcpSettings | Grupa właściwości, korzystając z narzędzia DistCp systemu plików HDFS. | Nie |
| resourceManagerEndpoint | Punkt końcowy usługi Yarn ResourceManager | Tak, jeśli korzystanie z narzędzia DistCp |
| tempScriptPath | Ścieżka folderu, używany do przechowywania temp skryptu polecenia DistCp. Plik skryptu jest generowany przez usługę Data Factory i zostaną usunięte po zakończeniu zadania kopiowania. | Tak, jeśli korzystanie z narzędzia DistCp |
| distcpOptions | Dodatkowe opcje przekazane polecenia DistCp. | Nie |

**Przykład: Źródło systemu plików HDFS w działania kopiowania przy użyciu zwolnienia**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Dowiedz się więcej na temat korzystania z narzędzia DistCp do skopiowania danych z systemu plików HDFS wydajnie w następnej sekcji.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopiowanie danych z systemu plików HDFS za pomocą narzędzia DistCp

[Narzędzia DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) natywnych narzędzie wiersza polecenia usługi Hadoop rozproszonych kopiowanie w klastrze usługi Hadoop. Po uruchomieniu polecenia Distcp najpierw wyświetlone zostaną wszystkie pliki do skopiowania, Utwórz kilka zadań mapy do klastra usługi Hadoop, a każde zadanie mapy wykona kopia binarna ze źródła do ujścia.

Kopiuj działania pomocy technicznej za pomocą narzędzia DistCp do kopiowania plików jako — do obiektów Blob platformy Azure (w tym [kopiowania etapowego](copy-activity-performance.md) lub Azure Data Lake Store, w którym to przypadku w pełni mogą korzystać power klastra, zamiast uruchamiać na środowiskiem Integration Runtime . Będzie ona większa przepustowość kopiowania, zwłaszcza, jeśli klaster jest bardzo zaawansowany Na podstawie konfiguracji w usłudze Azure Data Factory, działanie kopiowania automatycznie konstruowania polecenia distcp, Prześlij do klastra usługi Hadoop i monitorować stan kopiowania.

### <a name="prerequsites"></a>Prerequsites

Aby skopiować za pomocą narzędzia DistCp pliki jako — jest z systemu plików HDFS do obiektów Blob platformy Azure (w tym kopiowania przejściowego) lub Azure Data Lake Store, upewnij się, klastra Hadoop spełnia poniższe wymagania:

1. MapReduce i Yarn usługi są włączone.
2. Wersja usługi yarn jest 2.5 lub nowszej.
3. Serwer systemu plików HDFS jest zintegrowany z Twojego docelowego magazynu danych — Azure blob Storage lub Azure Data Lake Store:

    - Usługa Azure Blob systemu plików jest obsługiwany natywnie w od usługi Hadoop w wersji 2.7. Należy określić ścieżkę pliku jar w pliku konfiguracyjnym env usługi Hadoop.
    - System plików usługi Azure Data Lake Store jest spakowany, zaczynając od Hadoop 3.0.0-alpha1. Jeśli klaster Hadoop jest niższa niż ta wersja, należy ręcznie zaimportować związane z usługi ADLS jar pakietów (azure-datalake-store.jar) do klastra z [tutaj](https://hadoop.apache.org/releases.html)i określ ścieżkę pliku jar w pliku konfiguracyjnym env usługi Hadoop.

4. Przygotuj folderu tymczasowego w systemie HDFS. Ten folder tymczasowy jest używany do przechowywania skryptu powłoki DistCp, dlatego zajmują miejsce na poziomie KB.
5. Upewnij się, że konto użytkownika podane w połączonej usłudze systemu plików HDFS ma uprawnienia do) przesłać aplikację na platformie Yarn; (b) ma uprawnienia do tworzenia podfolder, Odczyt/zapis plików w obszarze powyżej folderu tymczasowego.

### <a name="configurations"></a>Konfiguracje

Poniżej przedstawiono przykład konfiguracji działania kopiowania do skopiowania danych z systemu plików HDFS do obiektów Blob platformy Azure za pomocą narzędzia DistCp:

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uwierzytelnianie Kerberos dla łącznika systemu plików HDFS

Dostępne są dwie opcje do skonfigurowania w lokalnym środowisku tak, aby korzystać z uwierzytelniania Kerberos w łączniku systemu plików HDFS. Można wybrać jeden lepiej odpowiada jego potrzebom tej sprawy.
* Opcja 1: [Dołączanie maszyny własne środowisko IR obszaru Kerberos](#kerberos-join-realm)
* Opcja 2: [Włącz wzajemnego zaufania między domeną Windows i protokół Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opcja 1: Dołączanie maszyny własne środowisko IR obszaru Kerberos

#### <a name="requirements"></a>Wymagania

* Maszyna środowiskiem Integration Runtime wymaga do dołączenia do obszaru protokołu Kerberos i nie można dołączyć do dowolnej domeny Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na komputerze środowiskiem Integration Runtime:**

1.  Uruchom **Ksetup** narzędzie, aby skonfigurować serwer Centrum dystrybucji KLUCZY Kerberos i obszaru.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszaru Kerberos różni się od domeny Windows. Można to osiągnąć przez ustawienie obszaru protokołu Kerberos i dodanie serwera KDC w następujący sposób. Zastąp *REALM.COM* z własnego obszaru odpowiednich zgodnie z potrzebami.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** maszyny po wykonaniu tych poleceń 2.

2.  Sprawdź konfigurację przy użyciu **Ksetup** polecenia. Dane wyjściowe powinny być takie jak:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W usłudze Azure Data Factory:**

* Konfigurowanie przy użyciu łącznika systemu plików HDFS **uwierzytelniania Windows** wraz z Twoja nazwa główna protokołu Kerberos i hasło, aby połączyć się ze źródłem danych systemu plików HDFS. Sprawdź [właściwości usługi połączonej systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

### <a name="kerberos-mutual-trust"></a>Opcja 2: Włącz wzajemnego zaufania między domeną Windows i protokół Kerberos

#### <a name="requirements"></a>Wymagania

*   Maszyny środowiskiem Integration Runtime należy przyłączyć do domeny Windows.
*   Musisz mieć uprawnienie można zaktualizować ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> Zamień REALM.COM i AD.COM w tym samouczku poniższy własnego obszaru odpowiednich a kontrolerem domeny, zgodnie z potrzebami.

**Na serwerze Centrum dystrybucji KLUCZY:**

1.  Edytuj konfigurację Centrum dystrybucji KLUCZY w **krb5.conf** pliku, aby umożliwić Centrum dystrybucji KLUCZY zaufania domeny Windows odwołujące się do następującego szablonu konfiguracji. Domyślnie konfiguracji znajduje się w **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Uruchom ponownie** Usługa Centrum dystrybucji KLUCZY, po przeprowadzeniu konfiguracji.

2.  Przygotowanie podmiotem zabezpieczeń o nazwie **krbtgt/REALM.COM@AD.COM** na serwerze Centrum dystrybucji KLUCZY za pomocą następującego polecenia:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  W **hadoop.security.auth_to_local** konfigurację usługi systemu plików HDFS Dodaj `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanowienia zaufania z domeny Windows do obszaru protokołu Kerberos. [hasło] jest hasłem do podmiotu zabezpieczeń **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżera serwera > Zarządzanie zasadami grupy > domeny > Obiekty zasad grupy > domyślny lub aktywnej domeny zasad i edycji.

    2. W **Edytor zarządzania zasadami grupy** okna podręcznego, przejdź do pozycji Konfiguracja komputera > zasady > Ustawienia Windows > Ustawienia zabezpieczeń > Zasady lokalne > Opcje zabezpieczeń i konfigurowanie **sieci zabezpieczenia: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć, gdy nawiązać Centrum dystrybucji KLUCZY. Często można po prostu wybierz odpowiednie opcje.

        ![Config typów szyfrowania dla protokołu Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Użyj **Ksetup** polecenie, aby określić algorytm szyfrowania dla określonego obszaru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowanie między konta domeny i nazwy głównej protokołu Kerberos, aby można było używać nazwy głównej protokołu Kerberos w domenie Windows.

    1. Uruchamianie narzędzi administracyjnych > **użytkownicy usługi Active Directory i komputery**.

    2. Konfiguruj funkcje zaawansowane, klikając **widoku** > **zaawansowane funkcje**.

    3. Znajdź konto, do której chcesz utworzyć mapowania i kliknij prawym przyciskiem myszy, aby wyświetlić **mapowań nazw** > kliknij **nazwy protokołu Kerberos** kartę.

    4. Dodaj nazwę główną z obszaru.

        ![Mapowanie tożsamości zabezpieczeń](media/connector-hdfs/map-security-identity.png)

**Na komputerze środowiskiem Integration Runtime:**

* Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W usłudze Azure Data Factory:**

* Konfigurowanie przy użyciu łącznika systemu plików HDFS **uwierzytelniania Windows** wraz z Twojego konta domeny lub nazwy głównej protokołu Kerberos do połączenia ze źródłem danych systemu plików HDFS. Sprawdź [właściwości usługi połączonej systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
