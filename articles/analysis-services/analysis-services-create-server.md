---
title: Szybki start — Tworzenie serwera usług Analysis Services przy użyciu witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć wystąpienie serwera usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ef4099130878813378fb277c45b5d352cbe822a7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000165"
---
# <a name="quickstart-create-a-server---portal"></a>Szybki start: Tworzenie serwera — Portal

W tym przewodniku Szybki start opisano, jak utworzyć zasób serwera usług Analysis Services w ramach subskrypcji platformy Azure, korzystając z portalu.

## <a name="prerequisites"></a>Wymagania wstępne 

* **Subskrypcja platformy Azure**: odwiedź stronę [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), aby utworzyć konto.
* **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory. Ponadto musisz zalogować się na platformie Azure przy użyciu konta należącego do tej dzierżawy usługi Azure Active Directory. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal 

[Logowanie się do portalu](https://portal.azure.com)


## <a name="create-a-server"></a>Tworzenie serwera

1. Kliknij kolejno pozycje **Utwórz zasób** > **Dane + analiza** > **Analysis Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. W sekcji **Analysis Services** wypełnij wymagane pola, a następnie naciśnij przycisk **Utwórz**.
   
   * **Nazwa serwera**: wpisz unikatową nazwę, która będzie używana do odwoływania się do serwera.
   * **Subskrypcja**: wybierz subskrypcję, z którą będzie skojarzony ten serwer.
   * **Grupa zasobów**: utwórz nową grupę zasobów lub wybierz istniejącą. Grupy zasobów ułatwiają zarządzanie kolekcją zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz [grupy zasobów](../azure-resource-manager/resource-group-overview.md).
   * **Lokalizacja**: lokalizacja centrum danych Azure, w którym będzie hostowany ten serwer. Wybierz lokalizację najbliższą względem Twojej największej bazy użytkowników.
   * **Warstwa cenowa**: Wybierz warstwę cenową. Jeśli testujesz tę funkcję i planujesz zainstalować przykładową modelową bazę danych, wybierz bezpłatną warstwę **D1**. Aby dowiedzieć się więcej, zobacz [cennik usług Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
    * **Administrator**: domyślnie jest to konto użyte do bieżącego logowania. Możesz wybrać inne konto w usłudze Azure Active Directory.
    * **Ustawienie Magazyn kopii zapasowych**: Opcjonalny. Jeśli masz już [konto magazynu](../storage/common/storage-introduction.md), możesz je wskazać jako domyślny magazyn kopii zapasowych modelowej bazy danych. Możesz również określić ustawienia [tworzenia i przywracania kopii zapasowych](analysis-services-backup.md) później.
    * **Czas wygaśnięcia klucza magazynu**: Opcjonalny. Określ czas wygaśnięcia klucza magazynu.

Tworzenie serwera zwykle trwa mniej niż minutę. W przypadku wybrania opcji **Dodaj do portalu** przejdź do portalu, aby zobaczyć nowy serwer. Możesz też wybrać pozycję **Wszystkie usługi** > **Analysis Services**, aby sprawdzić, czy serwer jest gotowy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy serwer nie będzie już potrzebny, usuń go. Na stronie **Przegląd** serwera kliknij pozycję **Usuń**. 

 ![Czyszczenie](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki Start przedstawiono sposób tworzenia serwera w ramach subskrypcji platformy Azure. Teraz, po utworzeniu serwera, możesz go zabezpieczyć, konfigurując (opcjonalną) zaporę serwera. Możesz też dodać do serwera podstawowy przykładowy model danych bezpośrednio w portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Szybki start: konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Samouczek: dodawanie przykładowego modelu do serwera](analysis-services-create-sample-model.md)
