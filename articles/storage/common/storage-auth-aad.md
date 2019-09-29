---
title: Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory | Microsoft Docs
description: Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: caf72d4af390956391fdab133cf0897abbee4633
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673157"
---
# <a name="authorize-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory

Usługa Azure Storage obsługuje używanie Azure Active Directory (AD) do autoryzacji żądań do magazynu obiektów blob i kolejek. Za pomocą usługi Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień podmiotowi zabezpieczeń, który może być użytkownikiem, grupą lub jednostką usługi aplikacji. Podmiot zabezpieczeń jest uwierzytelniany przez usługę Azure AD w celu zwrócenia tokenu OAuth 2,0. Token może służyć do autoryzowania żądania dostępu do zasobu w magazynie obiektów blob lub Queue.

Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2,0 zwróconego przez usługę Azure AD zapewnia najważniejsze zabezpieczenia i łatwość użycia w porównaniu z autoryzacją klucza współużytkowanego i sygnatury dostępu współdzielonego (SAS). W przypadku usługi Azure AD nie ma potrzeby przechowywania klucza dostępu do konta z kodem i ryzyka potencjalnej luki w zabezpieczeniach. Mimo że można nadal korzystać z autoryzacji klucza współużytkowanego w aplikacjach, korzystanie z usługi Azure AD powoduje obejście konieczności przechowywania klucza dostępu do konta w kodzie. Możesz również nadal używać sygnatur dostępu współdzielonego, aby udzielać szczegółowego dostępu do zasobów na koncie magazynu, ale usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami SAS ani martwić się o odwoływanie złamanych SAS. Jeśli to możliwe, firma Microsoft zaleca używanie autoryzacji usługi Azure AD z aplikacjami usługi Azure Storage.

Autoryzacja za pomocą usługi Azure AD jest dostępna dla wszystkich kont ogólnego przeznaczenia i magazynu obiektów BLOB we wszystkich regionach publicznych i w chmurach narodowych. Tylko konta magazynu utworzone za pomocą modelu wdrażania Azure Resource Manager obsługują autoryzację usługi Azure AD. Autoryzacja za pomocą usługi Azure AD nie jest obsługiwana w przypadku usługi Azure Table Storage.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Omówienie usługi Azure AD dla obiektów blob i kolejek

Gdy podmiot zabezpieczeń (użytkownik, Grupa lub aplikacja) próbuje uzyskać dostęp do zasobu obiektu BLOB lub kolejki, żądanie musi być autoryzowane, chyba że jest on obiektem BLOB dostępnym dla użytkowników anonimowych. W przypadku usługi Azure AD dostęp do zasobu jest procesem dwuetapowym. Najpierw jest uwierzytelniana tożsamość podmiotu zabezpieczeń i zwracany jest token OAuth 2,0. Następnie token jest przesyłany jako część żądania do obiektu BLOB lub usługa kolejki i używany przez usługę do autoryzacji dostępu do określonego zasobu.

Krok uwierzytelniania wymaga, aby aplikacja zażądała tokenu dostępu OAuth 2,0 w czasie wykonywania. Jeśli aplikacja jest uruchomiona w ramach jednostki platformy Azure, takiej jak maszyna wirtualna platformy Azure, zestaw skalowania maszyn wirtualnych lub aplikacja Azure Functions, może używać [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) do uzyskiwania dostępu do obiektów blob lub kolejek. Aby dowiedzieć się, jak autoryzować żądania wysyłane przez zarządzaną tożsamość do obiektu blob platformy Azure lub usługa kolejki, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md).

Krok autoryzacji wymaga, aby co najmniej jedna rola RBAC była przypisana do podmiotu zabezpieczeń. Usługa Azure Storage oferuje role RBAC, które obejmują wspólne zestawy uprawnień dla danych obiektów blob i kolejek. Role, które są przypisane do podmiotu zabezpieczeń, określają uprawnienia, które będą miały. Aby dowiedzieć się więcej na temat przypisywania ról RBAC dla usługi Azure Storage, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą RBAC](storage-auth-aad-rbac.md).

Natywne aplikacje i aplikacje sieci Web, które wysyłają żądania do obiektu blob platformy Azure lub usługa kolejki mogą również autoryzować dostęp za pomocą usługi Azure AD. Aby dowiedzieć się, jak zażądać tokenu dostępu i używać go do autoryzacji żądań dotyczących danych obiektów blob lub kolejek, zobacz temat [autoryzowanie dostępu do usługi Azure Storage za pomocą usługi Azure AD z aplikacji usługi Azure Storage](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Przypisywanie ról RBAC na potrzeby praw dostępu

Azure Active Directory (Azure AD) autoryzuje prawa dostępu do zabezpieczonych zasobów za pośrednictwem [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md). Usługa Azure Storage definiuje zestaw wbudowanych ról RBAC, które obejmują typowe zestawy uprawnień używane do uzyskiwania dostępu do danych obiektów blob i kolejek. Można także definiować role niestandardowe na potrzeby dostępu do danych obiektów blob i kolejek.

Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, konta magazynu lub pojedynczego kontenera lub kolejki. Podmiot zabezpieczeń usługi Azure AD może być użytkownikiem, grupą, główną usługą aplikacji lub [zarządzaną tożsamością dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Wbudowane role RBAC dla obiektów blob i kolejek

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Aby dowiedzieć się, jak przypisać wbudowaną rolę RBAC do podmiotu zabezpieczeń, zobacz jeden z następujących artykułów:

- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach w witrynie Azure Portal](storage-auth-aad-rbac-portal.md)
- [Udzielanie dostępu do danych w obiektach blob i kolejkach na platformie Azure za pomocą kontroli dostępu opartej na rolach przy użyciu interfejsu wiersza polecenia platformy Azure](storage-auth-aad-rbac-cli.md)
- [ przy użyciu programu PowerShell](storage-auth-aad-rbac-powershell.md)

Aby uzyskać więcej informacji o tym, jak wbudowane role są zdefiniowane dla usługi Azure Storage, zobacz [Omówienie definicji ról](../../role-based-access-control/role-definitions.md#management-and-data-operations). Aby uzyskać informacje na temat tworzenia niestandardowych ról RBAC, zobacz [Tworzenie ról niestandardowych dla Access Control opartej na rolach](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Uprawnienia dostępu do operacji na danych

Aby uzyskać szczegółowe informacje na temat uprawnień wymaganych do wywołania określonych operacji obiektu BLOB lub usługa kolejki, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Zakres zasobów

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Uzyskiwanie dostępu do danych za pomocą konta usługi Azure AD

Dostęp do danych obiektów blob lub kolejek za pośrednictwem Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure można autoryzować za pomocą konta usługi Azure AD użytkownika lub za pomocą kluczy dostępu do konta (autoryzacja klucza współdzielonego).

### <a name="data-access-from-the-azure-portal"></a>Dostęp do danych z Azure Portal

Azure Portal może korzystać z konta usługi Azure AD lub kluczy dostępu do konta w celu uzyskiwania dostępu do danych obiektów blob i kolejek na koncie usługi Azure Storage. Który schemat autoryzacji używany przez Azure Portal zależy od ról RBAC przypisanych do użytkownika.

Przy próbie uzyskania dostępu do danych obiektu BLOB lub kolejki Azure Portal najpierw sprawdza, czy przypisano rolę RBAC z **firmą Microsoft. Storage/storageAccounts/ListKeys/Action**. Jeśli przypisano rolę z tą akcją, Azure Portal używa klucza konta do uzyskiwania dostępu do danych obiektów blob i kolejek za pośrednictwem autoryzacji klucza wspólnego. Jeśli nie masz przypisanej roli z tą akcją, Azure Portal próbuje uzyskać dostęp do danych przy użyciu konta usługi Azure AD.

Aby uzyskać dostęp do danych obiektu BLOB lub kolejki z Azure Portal przy użyciu konta usługi Azure AD, musisz mieć uprawnienia dostępu do danych obiektów blob i kolejek, a także potrzebujesz uprawnień do nawigowania po zasobach konta magazynu w Azure Portal. Wbudowane role udostępniane przez usługę Azure Storage zapewniają dostęp do zasobów obiektów blob i kolejek, ale nie udzielają uprawnień do zasobów konta magazynu. Z tego powodu dostęp do portalu wymaga również przypisania roli Azure Resource Manager, takiej jak rola [czytnika](../../role-based-access-control/built-in-roles.md#reader) , w zakresie do poziomu konta magazynu lub wyższego. Rola **czytelnika** umożliwia dostęp do najbardziej ograniczonych uprawnień, ale można również uzyskać inną rolę Azure Resource Manager, która udziela dostępu do zasobów zarządzania kontami magazynu. Aby dowiedzieć się więcej na temat przypisywania uprawnień użytkownikom na potrzeby dostępu do danych w Azure Portal za pomocą konta usługi Azure AD, zobacz [udzielanie dostępu do obiektów blob platformy Azure i danych w kolejce przy użyciu RBAC w Azure Portal](storage-auth-aad-rbac-portal.md).

Azure Portal wskazuje, który schemat autoryzacji jest używany podczas przechodzenia do kontenera lub kolejki. Aby uzyskać więcej informacji o dostępie do danych w portalu, zobacz [używanie Azure Portal do uzyskiwania dostępu do danych obiektu BLOB lub kolejki](storage-access-blobs-queues-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Dostęp do danych z programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure i program PowerShell obsługują Logowanie przy użyciu poświadczeń usługi Azure AD. Po zalogowaniu się sesja zostanie uruchomiona w ramach tych poświadczeń. Aby dowiedzieć się więcej, zobacz [Uruchamianie interfejsu wiersza polecenia platformy Azure lub poleceń programu PowerShell przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych obiektu BLOB lub kolejki](storage-auth-aad-script.md)

## <a name="azure-ad-authorization-over-smb-for-azure-files"></a>Autoryzacja usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files

Azure Files obsługuje autoryzację w usłudze Azure AD za pośrednictwem protokołu SMB na potrzeby maszyn wirtualnych przyłączonych do domeny (wersja zapoznawcza) Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files, zobacz [omówienie Azure Active Directory autoryzacji za pośrednictwem protokołu SMB dla Azure Files (wersja zapoznawcz](../files/storage-files-active-directory-overview.md)

## <a name="next-steps"></a>Następne kroki

- [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu tożsamości Azure Active Directory i zarządzanych dla zasobów platformy Azure](storage-auth-aad-msi.md)
- [Autoryzuj przy użyciu Azure Active Directory z aplikacji w celu uzyskania dostępu do obiektów blob i kolejek](storage-auth-aad-app.md)
- [Obsługa usługi Azure Storage na potrzeby kontroli dostępu opartej na Azure Active Directory jest ogólnie dostępna](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
