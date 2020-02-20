---
title: Zarządzanie dostępem SSH dla kont domeny w usłudze Azure HDInsight
description: Kroki umożliwiające zarządzanie dostępem protokołu SSH dla kont usługi Azure AD w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472520"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Zarządzanie dostępem SSH dla kont domeny w usłudze Azure HDInsight

W bezpiecznych klastrach domyślnie wszyscy użytkownicy domeny w [usłudze Azure AD DS](../../active-directory-domain-services/overview.md) mogą używać protokołu [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) w węzłach głównych i brzegowych. Ci użytkownicy nie są częścią grupy sudo i nie uzyskują dostępu do katalogu głównego. Użytkownik SSH utworzony podczas tworzenia klastra będzie miał dostęp do katalogu głównego.

## <a name="manage-access"></a>Zarządzanie dostępem

Aby zmodyfikować dostęp SSH do określonych użytkowników lub grup, zaktualizuj `/etc/ssh/sshd_config` na każdym z węzłów.

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) do nawiązania połączenia z klastrem. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Otwórz plik `ssh_confi`g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Zmodyfikuj plik `sshd_config` zgodnie z potrzebami. W przypadku ograniczenia użytkowników do określonych grup konta lokalne nie mogą SSH do tego węzła. Poniżej przedstawiono tylko przykład składni:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Następnie Zapisz zmiany: **Ctrl + X**, **Y**, **Enter**.

1. Uruchom ponownie SSHD.

    ```bash
    sudo systemctl restart sshd
    ```

1. Powtórz powyższe kroki dla każdego węzła.

## <a name="ssh-authentication-log"></a>Dziennik uwierzytelniania SSH

Dziennik uwierzytelniania SSH jest zapisywana w `/var/log/auth.log`. Jeśli widzisz błędy logowania za pośrednictwem protokołu SSH dla kont lokalnych lub domenowych, musisz przejść przez dziennik, aby debugować błędy. Często problem może być związany z określonymi kontami użytkowników i zazwyczaj dobrym rozwiązaniem jest wypróbowanie innych kont użytkowników lub protokołów SSH przy użyciu domyślnego użytkownika SSH (konta lokalnego), a następnie próba narzędzie kinit.

## <a name="ssh-debug-log"></a>Dziennik debugowania SSH

Aby włączyć pełne rejestrowanie, należy ponownie uruchomić `sshd` z opcją `-d`. Podobnie jak `/usr/sbin/sshd -d` można również uruchomić `sshd` na porcie niestandardowym (np. 2222), aby nie trzeba było zatrzymać głównego demona SSH. Można również użyć opcji `-v` z klientem SSH, aby uzyskać więcej dzienników (widok po stronie klienta dla błędów).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu pakiet Enterprise Security](./apache-domain-joined-manage.md)
* [Łączenie z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
