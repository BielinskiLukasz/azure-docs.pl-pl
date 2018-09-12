---
title: 'Interfejs API REST: Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft'
description: Użyj interfejsów API REST WebHDFS do wykonywania operacji systemu plików w usłudze Azure Data Lake magazynu Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 62ecf3b1983853629f6bc5fd594231188aa67bcd
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391610"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operacje systemu plików w usłudze Azure Data Lake magazynu Gen1 przy użyciu interfejsu API REST
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Zestaw SDK Java](data-lake-store-get-started-java-sdk.md)
> * [Interfejs API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

W tym artykule dowiesz się, jak używać interfejsów API REST WebHDFS i interfejsów API REST Gen1 Data Lake Storage w celu wykonywania operacji systemu plików w usłudze Azure Data Lake magazynu Gen1. Aby uzyskać instrukcje na temat sposobu wykonywania operacji zarządzania kontem w Data Lake Storage Gen1 przy użyciu interfejsu API REST, zobacz [operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal](data-lake-store-get-started-portal.md).

* **[Adres cURL](http://curl.haxx.se/)**. W tym artykule używa programu cURL w celu zademonstrowania sposobu wykonywania wywołań interfejsu API REST względem konta Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>W jaki sposób uwierzytelniać za pomocą usługi Azure Active Directory?
Dostępne są dwa podejścia do uwierzytelniania za pomocą usługi Azure Active Directory.

* Aby uwierzytelnianie użytkowników końcowych (interakcyjne) aplikacji, zobacz [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Do uwierzytelniania service to service dla aplikacji (nieinterakcyjne), zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Tworzenie folderów
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Użyj następującego polecenia cURL. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

W poprzednim poleceniu zastąp ciąg \<`REDACTED`\> tokenem autoryzacji pobranym wcześniej. To polecenie tworzy katalog o nazwie **mytempdir** w folderze głównym konta usługi Data Lake Storage Gen1.

Jeśli operacja zakończy się pomyślnie, powinna pojawić się odpowiedź podobna do poniższego fragmentu kodu:

    {"boolean":true}

## <a name="list-folders"></a>Wyświetlanie listy folderów
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Użyj następującego polecenia cURL. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

W poprzednim poleceniu zastąp ciąg \<`REDACTED`\> tokenem autoryzacji pobranym wcześniej.

Jeśli operacja zakończy się pomyślnie, powinna pojawić się odpowiedź podobna do poniższego fragmentu kodu:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Przekazywanie danych
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Użyj następującego polecenia cURL. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

W poprzedniej składni parametr **-T** wskazuje na lokalizację przekazywanego pliku.

Dane wyjściowe będą podobne do następującego fragmentu kodu:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Odczyt danych
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Odczytu danych z usługi Data Lake Storage Gen1 konto jest procesem dwuetapowym.

* Najpierw prześlij żądanie GET względem punktu końcowego `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. To wywołanie zwraca lokalizację, do której należy przesłać kolejne żądanie GET.
* Następnie prześlij żądanie GET względem punktu końcowego `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. To wywołanie powoduje wyświetlenie zawartości pliku.

Jednak ponieważ nie ma żadnej różnicy w parametrach wejściowych kroku pierwszego i drugiego, możesz użyć parametru `-L` w celu przesłania pierwszego żądania. `-L` — ta opcja w praktyce łączy dwa żądania w jedno i powoduje, że narzędzie cURL ponawia żądanie dla nowej lokalizacji. Na koniec zostaną wyświetlone dane wyjściowe z wszystkich wywołań żądań, tak jak pokazano w poniższym fragmencie kodu. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Zmienianie nazwy pliku
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Użyj następującego polecenia cURL, aby zmienić nazwę pliku. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Usuwanie pliku
Ta operacja jest oparta na wywołaniu interfejsu API REST WebHDFS zdefiniowanym [tutaj](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Użyj następującego polecenia cURL, aby usunąć plik. Zastąp  **\<yourstorename >** nazwą konta usługi Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Powinny pojawić się dane wyjściowe podobne do następujących:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Kolejne kroki
* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu interfejsu API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Zobacz także
* [Dokumentacja interfejsu API REST programu usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Otwórz źródło danych big Data aplikacji zgodnych z usługi Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

