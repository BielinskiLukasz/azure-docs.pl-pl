---
title: Tworzenie dzierżawy usługi Azure AD na platformie Azure Red Hat OpenShift
description: Poniżej przedstawiono sposób tworzenia dzierżawy usługi Azure Active Directory (Azure AD), która będzie hostować klaster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381478"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Tworzenie dzierżawy usługi Azure AD na platformie Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift wymaga dzierżawy [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) , w której ma zostać utworzony klaster. *Dzierżawca* to dedykowane wystąpienie usługi Azure AD, które otrzymuje organizacja lub Deweloper aplikacji podczas tworzenia relacji z firmą Microsoft, rejestrując się na platformie Azure, Microsoft Intune lub Microsoft 365. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma własne tożsamości służbowe i rejestracje aplikacji.

Jeśli nie masz jeszcze dzierżawy usługi Azure AD, postępuj zgodnie z poniższymi instrukcjami, aby je utworzyć.

## <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Aby utworzyć dzierżawcę:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które chcesz skojarzyć z klastrem usługi Azure Red Hat OpenShift.
2. Otwórz [blok Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) , aby utworzyć nową dzierżawę (znaną również jako nowa *Azure Active Directory*).
3. Podaj **nazwę organizacji**.
4. Podaj **początkową nazwę domeny**. Zostanie do niego dołączony *onmicrosoft.com* . W tym miejscu możesz użyć tutaj wartości *Nazwa organizacji* .
5. Wybierz kraj lub region, w którym zostanie utworzona dzierżawa.
6. Kliknij przycisk **Utwórz**.
7. Po utworzeniu dzierżawy usługi Azure AD wybierz **pozycję kliknij tutaj, aby zarządzać nowym katalogiem** . Nową nazwę dzierżawy należy wyświetlić w prawym górnym rogu Azure Portal:  

    ![Zrzut ekranu przedstawiający Portal pokazujący nazwę dzierżawy w prawym górnym rogu][tenantcallout]  

8. Zanotuj *Identyfikator dzierżawy* , aby później można było określić miejsce utworzenia klastra usługi Azure Red Hat OpenShift. W portalu powinien zostać wyświetlony blok przegląd Azure Active Directory dla nowej dzierżawy. Wybierz pozycję **Właściwości** i skopiuj wartość **identyfikatora katalogu**. Ta wartość zostanie odwołująca się do `TENANT` w samouczku [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Zasoby

Zapoznaj się z [dokumentacją Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) , aby uzyskać więcej informacji o [dzierżawach usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć jednostkę usługi, wygenerować klucz tajny klienta i adres URL wywołania zwrotnego uwierzytelniania oraz utworzyć nowego użytkownika Active Directory na potrzeby testowania aplikacji w klastrze Red Hat OpenShift platformy Azure.

[Tworzenie obiektu i użytkownika aplikacji usługi Azure AD](howto-aad-app-configuration.md)
