---
title: Przykładowe dane w usłudze Azure Blob Storage — proces nauki danych zespołu
description: Próbkowanie danych przechowywanych w usłudze Azure Blob Storage przez pobranie go programowo, a następnie próbkowanie przy użyciu procedur utworzonych w języku Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720286"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Przykładowe dane w usłudze Azure Blob Storage

W tym artykule opisano dane próbkowania przechowywane w usłudze Azure Blob Storage przez pobranie go programowo, a następnie próbkowanie przy użyciu procedur utworzonych w języku Python.

**Dlaczego warto Przykładowo dane?**
Jeśli zestaw danych, który planujesz analizować jest duży, zazwyczaj dobrym pomysłem jest Przepróbkowanie danych w celu zmniejszenia ich do mniejszej, ale reprezentatywnej i większej możliwej do zarządzania wielkości. Próbkowanie ułatwia zrozumienie, eksplorację i inżynierowanie danych. Jej rolą w procesie Cortany Analytics jest umożliwienie szybkiego tworzenia prototypów funkcji przetwarzania danych i modeli uczenia maszynowego.

To zadanie próbkowania jest krokiem w [procesie nauki o danych zespołowych (przetwarzania TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Pobierz i zmniejsz przykładowe dane
1. Pobierz dane z usługi Azure Blob Storage przy użyciu Blob service z następującego przykładowego kodu w języku Python: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Odczytaj dane do ramki danych Pandas z pliku pobranego powyżej.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. W dół — przykładowe dane przy użyciu `numpy` `random.choice` :
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Teraz można korzystać z powyższej ramki danych z jednym procentowym przykładem do dalszej eksploracji i generowania funkcji.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Przekazywanie danych i odczytywanie ich w Azure Machine Learning
Możesz użyć następującego przykładowego kodu, aby w pełni próbkować dane i używać ich bezpośrednio w Azure Machine Learning:

1. Zapisz ramkę danych do pliku lokalnego
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Przekaż plik lokalny do obiektu blob platformy Azure przy użyciu następującego przykładowego kodu:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Odczytaj dane z obiektu blob platformy Azure przy użyciu Azure Machine Learning [Importuj dane](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , jak pokazano na poniższej ilustracji:

![Obiekt BLOB czytnika](./media/sample-data-blob/reader_blob.png)

