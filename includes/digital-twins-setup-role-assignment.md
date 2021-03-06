---
author: baanders
description: Dołączanie pliku dla kroku uprawnień dostępu w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478873"
---
Usługa Azure Digital bliźniaczych reprezentacji korzysta z [usługi Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) na potrzeby kontroli dostępu opartej na ROLACH (RBAC). Oznacza to, że zanim użytkownik będzie mógł dokonywać wywołań płaszczyzny danych do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, użytkownik musi mieć przypisaną rolę z odpowiednimi uprawnieniami.

W przypadku usługi Azure Digital bliźniaczych reprezentacji ta rola jest _**właścicielem danych usługi Azure Digital bliźniaczych reprezentacji**_. Więcej informacji o rolach i zabezpieczeniach można znaleźć w temacie [*pojęcia: zabezpieczenia dla rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](../articles/digital-twins/concepts-security.md).

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

W tej sekcji przedstawiono sposób tworzenia przypisania roli dla użytkownika w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, przy użyciu wiadomości e-mail tego użytkownika w dzierżawie usługi Azure AD w ramach subskrypcji platformy Azure. W zależności od roli w organizacji możesz skonfigurować to uprawnienie dla siebie lub skonfigurować je w imieniu kogoś innego, kto będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

### <a name="assign-the-role"></a>Przypisywanie roli

Aby przyznać użytkownikowi uprawnienia do zarządzania wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, należy przypisać im rolę _**właściciela danych Digital bliźniaczych reprezentacji platformy Azure**_ w ramach tego wystąpienia.

> [!NOTE]
> Należy zauważyć, że ta rola różni się od roli *właściciela* usługi Azure AD, która również może być przypisana w zakresie wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Są to dwie odrębne role zarządzania, a *właściciel* usługi Azure AD nie udziela dostępu do funkcji płaszczyzny danych przyznanych przez *właściciela danych Digital bliźniaczych reprezentacji systemu Azure*.