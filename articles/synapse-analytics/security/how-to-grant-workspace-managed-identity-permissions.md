---
title: Jak przyznać uprawnienia do tożsamości zarządzanej w obszarze roboczym usługi Azure Synapse
description: Artykuł objaśniający sposób konfigurowania uprawnień dla tożsamości zarządzanej w obszarze roboczym usługi Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 8e33bd127fca9ddce7b15d144fd287ec4a6bf0e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193468"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Przyznawanie uprawnień do tożsamości zarządzanej przez obszar roboczy (wersja zapoznawcza)

W tym artykule opisano sposób udzielania uprawnień zarządzanej tożsamości w obszarze roboczym usługi Azure Synapse. Uprawnienia z kolei umożliwiają dostęp do pul SQL w obszarze roboczym i ADLS Gen2 konta magazynu za pomocą Azure Portal.

>[!NOTE]
>Ta tożsamość zarządzana w obszarze roboczym będzie określana jako tożsamość zarządzana w pozostałej części tego dokumentu.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Przyznaj zarządzaną tożsamość do puli SQL

Zarządzana tożsamość przyznaje uprawnienia do pul SQL w obszarze roboczym. Z przyznanymi uprawnieniami można organizować potoki, które wykonują działania związane z pulą SQL. Podczas tworzenia obszaru roboczego usługi Azure Synapse przy użyciu Azure Portal można przyznać zarządzanym kontrolom kontroli tożsamości w pulach SQL.

Wybierz pozycję **zabezpieczenia i sieć** podczas tworzenia obszaru roboczego usługi Azure Synapse. Następnie wybierz pozycję **Udziel kontroli do zarządzanej tożsamości obszaru roboczego w PULACH SQL**.

![Uprawnienia KONTROLki dla pul SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Przyznawanie uprawnień tożsamości zarządzanej do ADLS Gen2 konta magazynu

Do utworzenia obszaru roboczego usługi Azure Synapse wymagane jest konto magazynu ADLS Gen2. Aby pomyślnie uruchomić pule platformy Spark w obszarze roboczym usługi Azure Synapse, tożsamość zarządzana Azure Synapse musi mieć rolę *współautor danych obiektów blob magazynu* na tym koncie magazynu. Ta rola ma również zalety aranżacji potoku w usłudze Azure Synapse.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Udziel uprawnień do tożsamości zarządzanej podczas tworzenia obszaru roboczego

Usługa Azure Synapse podejmie próbę przyznania roli współautor danych obiektów blob magazynu dla tożsamości zarządzanej po utworzeniu obszaru roboczego usługi Azure Synapse przy użyciu Azure Portal. Szczegóły konta magazynu ADLS Gen2 można podać na karcie **podstawy** .

![Karta podstawy w przepływie tworzenia obszaru roboczego](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Wybierz konto magazynu ADLS Gen2 i system plików w polu **nazwa konta** i **Nazwa systemu plików**.

![Podawanie szczegółowych informacji o koncie magazynu ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Jeśli twórca obszaru roboczego jest również **właścicielem** konta magazynu ADLS Gen2, usługa Azure Synapse przypisze rolę *współautor danych obiektów blob magazynu* do tożsamości zarządzanej. Zostanie wyświetlony następujący komunikat poniżej wprowadzonych szczegółów konta magazynu.

![Pomyślne przypisanie współautor danych obiektu blob magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Jeśli twórca obszaru roboczego nie jest właścicielem konta magazynu ADLS Gen2, usługa Azure Synapse nie przypisze roli *współautor danych obiektów blob magazynu* do tożsamości zarządzanej. Komunikat wyświetlany poniżej szczegóły konta magazynu powiadamia twórcę obszaru roboczego, że nie mają wystarczających uprawnień, aby przyznać roli *współautor danych obiektów blob magazynu* dla tożsamości zarządzanej.

![Niepowodzenie przypisania współautor danych obiektu blob magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Jako komunikat Stany nie można tworzyć pul platformy Spark, chyba że *współautor danych obiektu blob magazynu* zostanie przypisany do zarządzanej tożsamości.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Udziel uprawnień do tożsamości zarządzanej po utworzeniu obszaru roboczego

Jeśli podczas tworzenia obszaru roboczego nie przypiszesz *współautora danych obiektu blob magazynu* do tożsamości zarządzanej, **właściciel** konta magazynu ADLS Gen2 ręcznie przypisze tę rolę do tożsamości. Poniższe kroki pomogą Ci wykonać przypisanie ręczne.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Krok 1: Przejdź do konta magazynu ADLS Gen2 w programie Azure Portal

W Azure Portal Otwórz konto magazynu ADLS Gen2 i wybierz pozycję **Przegląd** na lewym pasku nawigacyjnym. Wystarczy przypisać rolę *współautor danych obiektów blob magazynu* na poziomie kontenera lub systemu plików. Wybierz **kontenery**.  
![ADLS Gen2 konta magazynu — Omówienie](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Krok 2. Wybieranie kontenera

Tożsamość zarządzana powinna mieć dostęp do danych kontenera (systemu plików), który został dostarczony podczas tworzenia obszaru roboczego. Ten kontener lub system plików można znaleźć w Azure Portal. Otwórz obszar roboczy usługi Azure Synapse w Azure Portal i wybierz kartę **Przegląd** w lewym okienku nawigacji.
![Kontener konta magazynu ADLS Gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Wybierz ten sam kontener lub system plików, aby przyznać roli *współautor danych obiektów blob magazynu* dla tożsamości zarządzanej.
![ADLS Gen2 wybór kontenera konta magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Krok 3. przechodzenie do kontroli dostępu

Wybierz pozycję **Access Control (IAM)**.

![Kontrola dostępu (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Krok 4. Dodawanie nowego przypisania roli

Wybierz pozycję **+ Dodaj**.

![Dodaj nowe przypisanie roli](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Krok 5. Wybieranie roli RBAC

Wybierz rolę **współautor danych obiektu blob magazynu** .

![Wybierz rolę RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Krok 6. Wybieranie podmiotu zabezpieczeń usługi Azure AD

Z listy rozwijanej **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD** .

![Wybieranie podmiotu zabezpieczeń usługi AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Krok 7. Wyszukiwanie tożsamości zarządzanej

Nazwa tożsamości zarządzanej jest również nazwą obszaru roboczego. Wyszukaj swoją tożsamość zarządzaną, wprowadzając nazwę obszaru roboczego usługi Azure Synapse w obszarze **Wybierz**. Powinna zostać wyświetlona wyświetlana tożsamość zarządzana.

![Znajdowanie tożsamości zarządzanej](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Krok 8. Wybieranie tożsamości zarządzanej

Wybierz zarządzaną tożsamość do **wybranych członków**. Wybierz pozycję **Zapisz** , aby dodać przypisanie roli.

![Wybierz zarządzaną tożsamość](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Krok 9. sprawdzenie, czy rola współautor danych obiektów blob magazynu jest przypisana do zarządzanej tożsamości

Wybierz pozycję **Access Control (IAM)** , a następnie wybierz pozycję **przypisania ról**.

![Weryfikuj przypisanie roli](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Tożsamość zarządzana powinna zostać wyświetlona w sekcji **współautor danych obiektów blob magazynu** z przypisaną rolą *współautor danych obiektów blob magazynu* . 
![ADLS Gen2 wybór kontenera konta magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [tożsamości zarządzanej przez obszar roboczy](./synapse-workspace-managed-identity.md)
