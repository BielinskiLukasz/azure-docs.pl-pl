---
title: Azure Quickstart — przekazywanie, pobieranie i wyświetlanie listy obiektów blob w usłudze Azure Storage za pomocą języka Ruby | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz konto magazynu i kontener. Następnie przy użyciu biblioteki klienta języka Ruby przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz listę obiektów blob w kontenerze.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: seguler
ms.openlocfilehash: 493da815b670c15bb892fa0be750c684cab70cb9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą języka Ruby

Dzięki tej skróconej instrukcji dowiesz się, w jaki sposób za pomocą języka Ruby przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start: 
* Instalowanie języka [Ruby](https://www.ruby-lang.org/en/downloads/)
* Zainstaluj [bibliotekę usługi Azure Storage dla języka Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) za pomocą pakietu rubygem. 

```
gem install azure-storage-blob
```

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji
[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Ruby.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć przykładową aplikację w języku Ruby, wyszukaj folder storage-blobs-ruby-quickstart, a następnie otwórz plik example.rb.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
W aplikacji należy podać nazwę konta magazynu i klucz konta, aby utworzyć wystąpienie `BlobService` na potrzeby aplikacji. Otwórz plik `example.rb` w Eksploratorze rozwiązań w środowisku IDE. Zastąp wartości **accountname** i **accountkey** własną nazwą konta i własnym kluczem. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Ta aplikacja przykładowa tworzy plik testowy w folderze „Documents”. Aplikacja przykładowa przesyła plik testowy do usługi Blob Storage, wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą. 

Uruchom przykład. Poniższej przedstawiono przykładowe dane wyjściowe zwracane po uruchomieniu aplikacji:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Po naciśnięciu dowolnego klawisza w celu kontynuowania aplikacja przykładowa usunie kontener magazynu i pliki. Przed kontynuowaniem sprawdź, czy w folderze „Documents” znajdują się te dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](http://storageexplorer.com). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe. Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik example.rb i przyjrzyj się kodowi. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu
Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlobService** usługi Azure Storage, aby skonfigurować poświadczenia połączenia. 
* Utwórz obiekt **Container** reprezentujący kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.

Gdy istnieje już kontener Cloud Blob, możesz utworzyć obiekt blob **Block** wskazujący konkretny obiekt blob, który Cię interesuje, i wykonywać operacje takie jak przekazywanie, pobieranie i kopiowanie.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ta sekcja poświęcona jest konfigurowaniu wystąpienia klienta usługi Azure Storage, tworzeniu wystąpienia obiektu usługi Blob, tworzeniu nowego kontenera, a następnie ustawianiu uprawnień w kontenerze, aby obiekty blob były publiczne. Kontener nazywa się **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start.  

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody **create\_block\_blob()**. 

Za pomocą przykładowego kodu tworzony jest plik lokalny do zastosowania w przypadku przekazywania i pobierania. Plik do przekazania jest przechowany jako **file\_path\_to\_file**, a nazwa obiektu blob jako **local\_file\_name**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Aby wykonać częściową aktualizację zawartości blokowego obiektu blob, użyj metody **create\_block\_list()**. Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Stronicowe obiekty blob są używane głównie do tworzenia plików VHD służących do obsługi maszyn wirtualnych IaaS. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Uzupełnianego obiektu blob należy używać w ramach pojedynczego modelu zapisywania. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze, używając metody **list\_blobs()**. Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając nazwy obiektów blob odnalezionych w kontenerze.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na dysk lokalny, używając metody **get\_blob()**. Poniższy kod pozwala pobrać obiekt blob przekazany w poprzedniej sekcji. Ciąg „_DOWNLOADED” jest dodawany jako sufiks do nazwy obiektu blob, co pozwala zobaczyć oba pliki na dysku lokalnym. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener, korzystając z metody **delete\_container()**. Jeśli utworzone pliki nie są już potrzebne, możesz użyć metody **delete\_blob()**, aby je usunąć.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Ruby z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji Ruby z magazynem obiektów blob:

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Ruby](https://github.com/Azure/azure-storage-ruby) dla usługi Azure Storage i pobierz go.
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) napisanymi przy użyciu biblioteki klienta Ruby.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka Ruby. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do instrukcji dotyczących magazynu obiektów blob.

> [!div class="nextstepaction"]
> [Instrukcje: Operacje wykonywane w usłudze Blob Storage](./storage-ruby-how-to-use-blob-storage.md)


Aby uzyskać więcej informacji na temat Eksploratora usługi Storage i obiektów blob, zapoznaj się artykułem [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Microsoft Azure Storage).
