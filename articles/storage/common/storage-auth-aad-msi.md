---
title: Uwierzytelnianie dostępu do obiektów blob i kolejki przy użyciu tożsamości usługi Azure Active Directory zarządzane dla zasobów platformy Azure (wersja zapoznawcza) — usługi Azure Storage | Dokumentacja firmy Microsoft
description: Magazynu obiektów Blob i kolejek platformy Azure obsługuje uwierzytelnianie usługi Azure Active Directory za pomocą tożsamości zarządzanych zasobów platformy Azure. Zarządzanych tożsamości dla zasobów platformy Azure służy do uwierzytelniania dostępu do obiektów blob i kolejki z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure, aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych. Korzystając z zarządzanych tożsamości dla zasobów platformy Azure i możliwości usługi uwierzytelniania usługi Azure AD, można uniknąć przechowywania poświadczeń za pomocą aplikacji działających w chmurze.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 15c37be3f3b1b3f72c32865e095091fa10ee9750
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251694"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources-preview"></a>Uwierzytelnianie dostępu do obiektów blob i kolejki z zarządzanych tożsamości dla zasobów platformy Azure (wersja zapoznawcza)

Usługa Azure storage Blob i kolejki obsługuje uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure może uwierzytelnić dostęp do obiektów blob i kolejki, przy użyciu poświadczeń usługi Azure AD z aplikacjami uruchomionymi na maszynach wirtualnych platformy Azure (maszyny wirtualne), aplikacji funkcji, zestawy skalowania maszyn wirtualnych i innych. Korzystając z zarządzanych tożsamości dla zasobów platformy Azure i możliwości usługi uwierzytelniania usługi Azure AD, można uniknąć przechowywania poświadczeń za pomocą aplikacji działających w chmurze.  

Aby udzielić uprawnień do tożsamości zarządzanej do kontenera obiektów blob i kolejki, możesz przypisać rolę kontroli dostępu opartej na rolach do tożsamości zarządzanej, który obejmuje uprawnienia dla tego zasobu w zakresie odpowiednie. Aby uzyskać więcej informacji na temat ról RBAC w magazynie, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md). 

W tym artykule przedstawiono sposób uwierzytelniania do magazynu obiektów Blob platformy Azure lub kolejki przy użyciu tożsamości zarządzanej maszyny wirtualnej platformy Azure.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>Włącz zarządzanych tożsamości na maszynie Wirtualnej

Zanim zarządzanych tożsamości dla zasobów platformy Azure można użyć do uwierzytelniania dostępu do obiektów blob i kolejki z maszyny Wirtualnej, należy najpierw włączyć zarządzanych tożsamości dla zasobów platformy Azure na maszynie Wirtualnej. Aby dowiedzieć się, jak włączyć zarządzanych tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Zestawy SDK platformy Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>Przypisz rolę RBAC do tożsamości usługi Azure AD, zarządzane

Aby uwierzytelniać się tożsamość zarządzana z poziomu aplikacji usługi Azure Storage, należy najpierw skonfigurować ustawienia kontroli (RBAC) dostępu opartej na rolach dla tej tożsamości zarządzanej. Usługa Azure Storage określa role RBAC, które obejmują uprawnienia do kontenerów i kolejek. Gdy rola RBAC jest przypisany do tożsamości zarządzanej, zarządzać tożsamościami udzielany jest dostęp do tego zasobu. Aby uzyskać więcej informacji, zobacz [Zarządzaj praw dostępu do danych obiektów Blob platformy Azure i kolejek przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

## <a name="get-a-managed-identity-access-token"></a>Uzyskiwanie tokenu dostępu tożsamości zarządzanej

Aby uwierzytelniać się za pomocą tożsamości zarządzanej, aplikacji lub skryptu należy uzyskać token dostępu tożsamość zarządzaną. Aby dowiedzieć się więcej o tym, jak można uzyskać tokenu dostępu, zobacz [jak uzyskiwanie tokenu dostępu, za pomocą tożsamości zarządzanych zasobów platformy Azure na Maszynie wirtualnej platformy Azure](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Przykładowy kod platformy .NET: Utwórz blokowy obiekt blob

W przykładzie kodu założono, że token dostępu tożsamość zarządzaną. Token dostępu jest używany do autoryzowania tożsamości zarządzanej, aby utworzyć blokowego obiektu blob.

### <a name="add-references-and-using-statements"></a>Dodaj odwołania i przy użyciu instrukcji  

W programie Visual Studio należy zainstalować wersję zapoznawczą biblioteki klienta usługi Azure Storage. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**. W konsoli, należy wpisać następujące polecenie:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

Dodaj następujące instrukcje using do kodu:

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>Utwórz poświadczenia z tożsamości zarządzanej tokenu dostępu

Aby utworzyć blokowego obiektu blob, użyj **TokenCredentials** klasy dostarczane przez pakiet (wersja zapoznawcza). Utworzyć nowe wystąpienie klasy **TokenCredentials**, przekazując tożsamość zarządzaną token dostępu uzyskany wcześniej:

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby dowiedzieć się, jak autoryzować dostęp do kontenerów i kolejki ze w aplikacjach pamięci masowej, zobacz [Użyj usługi Azure AD z magazynu aplikacji](storage-auth-aad-app.md).
- Aby dowiedzieć się, jak zalogować się do wiersza polecenia platformy Azure i programu PowerShell przy użyciu tożsamości usługi Azure AD, zobacz [tożsamości usługi Azure AD umożliwia dostęp do usługi Azure Storage przy użyciu interfejsu wiersza polecenia lub programu PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob platformy Azure, zobacz blog zespołu usługi Azure Storage, publikowania, [ogłoszenie uwierzytelniania w wersji zapoznawczej programu Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
