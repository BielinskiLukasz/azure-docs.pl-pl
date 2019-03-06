---
title: Przesyłanie przepływu pracy przy użyciu danych wejściowych — plik BAM Microsoft Genomics
titleSuffix: Azure
description: W tym artykule przyjęto założenie, mieć zainstalowanego klienta msgen i pomyślnie uruchomił przykładowe dane za pośrednictwem usługi.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 329ac374edbffa65a1b654a05a4934828f25d5de
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435087"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Przesyłanie przepływu pracy przy użyciu pliku wejściowego BAM

W tym artykule przedstawiono sposób przesyłania przepływu pracy do usługi Microsoft Genomics, jeśli plik wejściowy to pojedynczy plik BAM. W tym temacie założono, że użytkownik zainstalował i uruchomił klienta `msgen` oraz że zna sposób korzystania z usługi Azure Storage. Jeśli zostało pomyślnie przesłane przepływu pracy przy użyciu podanych przykładowych danych, można przystąpić do kontynuować z tego artykułu. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Konfiguracja: Przekazywanie pliku BAM do usługi Azure storage
Załóżmy, że mamy jeden plik BAM, *reads.bam*, oraz że przekazano go do konta magazynu *myaccount* na platformie Azure jako obiekt **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. Masz adres URL interfejsu API i klucz dostępu. Chcesz uzyskać dane wyjściowe w lokalizacji **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Przesyłanie zadania do klienta `msgen` 


Oto minimalny zestaw argumentów, jakie należy podać do klienta `msgen`; podziały wierszy zostały dodane z myślą o przejrzystości:

W przypadku systemu Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


W przypadku systemu Unix

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Jeśli wolisz używać pliku konfiguracji, oto jego zawartość:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Prześlij plik `config.txt` przy użyciu tego wywołania: `msgen submit -f config.txt`

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przekazano plik BAM do usługi Azure Storage oraz przesłano przepływ pracy do usługi Microsoft Genomics za pośrednictwem klienta `msgen` Python. Aby uzyskać dodatkowe informacje o przesyłaniu przepływów pracy i innych poleceniach, których możesz użyć wraz z usługą Microsoft Genomics, zobacz [często zadawane pytania](frequently-asked-questions-genomics.md). 
