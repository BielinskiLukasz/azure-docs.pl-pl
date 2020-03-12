---
title: Przekazywanie danych obrazu do chmury za pomocą usługi Azure Storage | Microsoft Docs
description: Przechowywanie danych aplikacji przy użyciu usługi Azure Blob Storage i aplikacji internetowej
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: 49078d2f374203a9fab4fe0f5e3881f6b1b22959
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130330"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Samouczek: przekazywanie danych obrazu do chmury za pomocą usługi Azure Storage

Niniejszy samouczek jest pierwszą częścią serii. W tym samouczku dowiesz się, jak wdrożyć aplikację sieci Web, która korzysta z biblioteki klienta usługi Azure Blob Storage w celu przekazywania obrazów do konta magazynu. Po zakończeniu tego samouczka będziesz mieć aplikację internetową zapisującą obrazy w usłudze Azure Storage i wyświetlającą obrazy z tej usługi.

# <a name="net-v12-sdk"></a>[\..NET V12 SDK](#tab/dotnet)
![Aplikacja do zmiany rozmiaru obrazu w programie .NET](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Zestaw SDK v10 środowiska Node. js](#tab/nodejsv10)
![Aplikacja do zmiany rozmiaru obrazu w Node. js v10](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta magazynu
> * Tworzenie kontenera i ustawianie uprawnień
> * Pobieranie klucza dostępu
> * Wdrażanie aplikacji internetowej na platformie Azure
> * Konfigurowanie ustawień aplikacji
> * Korzystanie z aplikacji internetowej

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka potrzebna jest subskrypcja platformy Azure. Zanim rozpoczniesz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Utwórz grupę zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.  

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Przykład przekazuje obrazy do kontenera obiektów BLOB na koncie usługi Azure Storage. Konto magazynu zapewnia unikatową przestrzeń nazw do przechowywania i umożliwiania dostępu do obiektów danych usługi Azure Storage. Utwórz konto magazynu w utworzonej grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> W części 2 samouczka użyjesz usługi Azure Event Grid z usługą Blob Storage. Pamiętaj, aby utworzyć konto magazynu w regionie świadczenia usługi Azure, który obsługuje usługę Event Grid. Lista obsługiwanych regionów znajduje się w artykule [Produkty platformy Azure według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

W poniższym poleceniu zastąp symbol zastępczy `<blob_storage_account>` swoją własną unikatową w skali globalnej nazwą konta usługi Blob Storage.

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Tworzenie kontenerów w usłudze Blob Storage

Aplikacja używa dwóch kontenerów w ramach konta usługi Blob Storage. Kontenery są podobne do folderów i służą do przechowywania obiektów blob. Z kontenerem *images* mamy do czynienia wtedy, gdy aplikacja przekazuje obrazy w pełnej rozdzielczości. W dalszej części serii aplikacja funkcji platformy Azure będzie przekazywać miniatury obrazów o zmienionym rozmiarze do kontenera *thumbnails*.

Pobierz klucz konta magazynu przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys). Następnie użyj tego klucza do utworzenia dwóch kontenerów za pomocą polecenia [az storage container create](/cli/azure/storage/container).

Dostęp publiczny do kontenera *images* jest ustawiony na wartość `off`. Dostęp publiczny do kontenera *thumbnails* jest ustawiony na wartość `container`. Ustawienie dostępu publicznego na wartość `container` powoduje, że użytkownicy, którzy odwiedzają stronę internetową, mogą wyświetlać miniatury.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Zanotuj nazwę konta usługi Blob Storage i klucz. Przykładowa aplikacja korzysta z tych ustawień, aby nawiązywać połączenie z kontem magazynu w celu przekazywania obrazów. 

## <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[Plan usługi App Service](../../app-service/overview-hosting-plans.md) określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która jest hostem aplikacji.

Utwórz plan usługi App Service za pomocą polecenia [az appservice plan create](/cli/azure/appservice/plan).

W poniższym przykładzie jest tworzony plan usługi App Service o nazwie `myAppServicePlan` przy użyciu warstwy cenowej **Bezpłatna**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Aplikacja internetowa zapewnia obszar hostingu dla kodu aplikacji przykładowej, który jest wdrażany z przykładowego repozytorium usługi GitHub. Utwórz [aplikację internetową](../../app-service/overview.md) w `myAppServicePlan`planie usługi App Service za pomocą polecenia [az webapp create](/cli/azure/webapp).  

W poniższym poleceniu zastąp ciąg `<web_app>` unikatową nazwą. Prawidłowe znaki to `a-z`, `0-9` i `-`. Jeśli nazwa `<web_app>` nie jest unikatowa, zostanie wyświetlony komunikat o błędzie *Witryna internetowa o nazwie `<web_app>` już istnieje.* Domyślnym adresem URL aplikacji internetowej jest `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Wdrażanie aplikacji przykładowej z repozytorium usługi GitHub

# <a name="net-v12-sdk"></a>[\..NET V12 SDK](#tab/dotnet)

Usługa App Service obsługuje kilka metod wdrażania zawartości w aplikacji internetowej. W tym samouczku wdrażasz aplikację internetową z [publicznego repozytorium przykładów usługi GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Skonfiguruj wdrożenie usługi GitHub do aplikacji internetowej za pomocą polecenia [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Przykładowy projekt zawiera aplikację [ASP.NET MVC](https://www.asp.net/mvc). Akceptuje ona obraz, zapisuje go na koncie magazynu i wyświetla obrazy z kontenera miniatur. Aplikacja sieci Web używa przestrzeni nazw [Azure. Storage](/dotnet/api/azure.storage), [Azure. Storage. blob](/dotnet/api/azure.storage.blobs)i [Azure. Storage. Blobs. models](/dotnet/api/azure.storage.blobs.models) w celu współdziałania z usługą Azure Storage.

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Zestaw SDK v10 środowiska Node. js](#tab/nodejsv10)
Usługa App Service obsługuje kilka metod wdrażania zawartości w aplikacji internetowej. W tym samouczku wdrażasz aplikację internetową z [publicznego repozytorium przykładów usługi GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Skonfiguruj wdrożenie usługi GitHub do aplikacji internetowej za pomocą polecenia [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

---

## <a name="configure-web-app-settings"></a>Konfigurowanie ustawień aplikacji internetowej

# <a name="net-v12-sdk"></a>[\..NET V12 SDK](#tab/dotnet)

Przykładowa aplikacja internetowa używa [interfejsów API usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage) do przekazywania obrazów. Poświadczenia konta magazynu są ustawiane w ustawieniach aplikacji dla aplikacji sieci Web. Dodaj ustawienia aplikacji do wdrożonej aplikacji za pomocą polecenia [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Zestaw SDK v10 środowiska Node. js](#tab/nodejsv10)

Przykładowa aplikacja internetowa używa [biblioteki klienta usługi Azure Storage](https://github.com/Azure/azure-storage-js), aby żądać tokenów dostępu, które służą do przekazywania obrazów. Poświadczenia konta magazynu używane przez zestaw Storage SDK są konfigurowane w ustawieniach aplikacji dla danej aplikacji internetowej. Dodaj ustawienia aplikacji do wdrożonej aplikacji za pomocą polecenia [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Po wdrożeniu i skonfigurowaniu aplikacji internetowej możesz przetestować funkcję przekazywania obrazów w aplikacji.

## <a name="upload-an-image"></a>Przekazywanie obrazu

Aby przetestować aplikację internetową, przejdź pod adres URL Twojej opublikowanej aplikacji. Domyślnym adresem URL aplikacji internetowej jest `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\..NET V12 SDK](#tab/dotnet)

Wybierz region **Przekaż Zdjęcia** , aby określić i przekazać plik, lub przeciągnij plik na region. Obraz zniknie, jeśli zostanie pomyślnie przekazany. Sekcja **Generated Thumbnails** (Wygenerowane miniatury) pozostanie pusta, dopóki nie przetestujemy jej w dalszej części tego tematu.

![Przekazywanie zdjęć w programie .NET](media/storage-upload-process-images/figure1.png)

W przykładowym kodzie zadanie `UploadFileToStorage` w pliku *Storagehelper.cs* służy do przekazywania obrazów do kontenera *obrazy* na koncie magazynu przy użyciu metody [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) . Poniższy przykładowy kod zawiera zadanie `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

W poprzednim zadaniu użyto następujących klas i metod:

| Klasa    | Metoda   |
|----------|----------|
| [Adresu](/dotnet/api/system.uri) | [Konstruktor identyfikatora URI](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (String, String) — Konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Zestaw SDK v10 środowiska Node. js](#tab/nodejsv10)

Wybierz pozycję **Choose File** (Wybierz plik), aby wybrać plik, a następnie kliknij przycisk **Upload Image** (Przekaż obraz). Sekcja **Generated Thumbnails** (Wygenerowane miniatury) pozostanie pusta, dopóki nie przetestujemy jej w dalszej części tego tematu. 

![Przekazywanie zdjęć w programie Node. js v10](media/storage-upload-process-images/upload-app-nodejs.png)

W kodzie przykładowym trasa `post` odpowiada za przekazanie obrazu do kontenera obiektów blob. Podczas przekazywania trasa korzysta z następujących modułów:

- [multer](https://github.com/expressjs/multer) — implementuje strategię przekazywania dla programu kierującego.
- [into-stream](https://github.com/sindresorhus/into-stream) — konwertuje bufor na strumień zgodnie z wymogami metody [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

Po przesłaniu pliku do trasy zawartość pliku pozostaje w pamięci do momentu przekazania pliku do kontenera obiektów blob.

> [!IMPORTANT]
> Ładowanie dużych plików do pamięci może mieć negatywny wpływ na wydajność aplikacji internetowej. Jeśli spodziewasz się, że użytkownicy będą przekazywać duże pliki, możesz rozważyć tymczasowe umieszczanie plików w systemie plików serwera internetowego, a następnie zaplanowanie operacji przekazania do usługi Blob Storage. Gdy pliki znajdą się w usłudze Blob Storage, będzie można usunąć je z systemu plików serwera.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Sprawdzanie, czy obraz jest wyświetlany na koncie magazynu

Zaloguj się do [Azure portal](https://portal.azure.com). Z menu po lewej stronie wybierz pozycję **Konta magazynu**, a następnie wybierz nazwę swojego konta magazynu. Wybierz **kontenery, a**następnie wybierz kontener **obrazy** .

Sprawdź, czy obraz jest wyświetlany w kontenerze.

![Lista kontenerów obrazów w witrynie Azure Portal](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testowanie wyświetlania miniatur

W celu przetestowania wyświetlania miniatur należy przekazać obraz do kontenera **thumbnails**, aby sprawdzić, czy aplikacja ma możliwość odczytywania z kontenera **thumbnails**.

Zaloguj się do [Azure portal](https://portal.azure.com). Z menu po lewej stronie wybierz pozycję **Konta magazynu**, a następnie wybierz nazwę swojego konta magazynu. Wybierz **kontenery**, a następnie wybierz kontener thumbnails ( **miniatury** ). Wybierz pozycję **Przekaż**, aby otworzyć okienko **Przekazywanie obiektu blob**.

Wybierz plik za pomocą selektora plików i wybierz pozycję **Przekaż**.

Przejdź z powrotem do aplikacji, aby sprawdzić, czy obraz przekazany do kontenera **thumbnails** jest widoczny.

# <a name="net-v12-sdk"></a>[\..NET V12 SDK](#tab/dotnet)
![Aplikacja do zmiany rozmiaru obrazu platformy .NET z wyświetlonym nowym obrazem](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Zestaw SDK v10 środowiska Node. js](#tab/nodejsv10)
![Aplikacja do zmiany rozmiaru obrazu v10 Node. js z nowym obrazem](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

W drugiej części tej serii zautomatyzujesz proces tworzenia obrazów miniatur, dlatego nie potrzebujesz tego obrazu. W kontenerze **thumbnails** w witrynie Azure Portal zaznacz przekazany obraz i wybierz pozycję **Usuń**, aby usunąć obraz. 

Możesz włączyć Content Delivery Network (CDN) w celu buforowania zawartości z konta usługi Azure Storage. Aby uzyskać więcej informacji o sposobie włączania sieci CDN dla konta usługi Azure Storage, zobacz [Integracja konta magazynu platformy Azure z usługą Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Następne kroki

W części pierwszej tej serii pokazano, jak skonfigurować aplikację internetową do interakcji z magazynem.

Przejdź do drugiej części w tej serii, aby dowiedzieć się, jak za pomocą usługi Event Grid wyzwolić funkcję platformy Azure umożliwiającą zmianę rozmiaru obrazu.

> [!div class="nextstepaction"]
> [Wyzwalanie funkcji platformy Azure umożliwiającej zmianę rozmiaru obrazu przy użyciu usługi Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
