<properties 
    pageTitle="Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ" 
    description="このページでは、Azure MFA とサード パーティ製品の段階的設定構成について紹介します。" 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="billmath"/>


# Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ

Azure Multi-Factor Authentication を利用し、さまざまなサード パーティ VPN ソリューションとシームレスに接続できます。 たとえば、Cisco® ASA VPN アプライアンス、Citrix NetScaler SSL VPN アプライアンス、Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN アプライアンスをご利用いただけます。

## Cisco ASA VPN アプライアンスと Azure Multi-Factor Authentication

Azure Multi-Factor Authentication は Cisco® ASA VPN アプライアンスとシームレスに統合し、Cisco AnyConnect® VPN のログインとポータル アクセスのセキュリティを強化します。 これは LDAP または RADIUS プロトコルを利用して実行できます。 次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

 構成ガイド| 説明
------------- | ------------- |
 [Cisco ASA に LDAP の Anyconnect VPN と Azure MFA の構成](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx)| Cisco ASA VPN アプライアンスと Azure MFA を LDAP でシームレスに統合します|
 [Cisco ASA radius Anyconnect VPN と Azure MFA の構成](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx)| Cisco ASA VPN アプライアンスと Azure MFA を RADIUS でシームレスに統合します

## Citrix NetScaler SSL VPN と Azure Multi-Factor Authentication

Azure Multi-Factor Authentication は Citrix NetScaler SSL VPN アプライアンスとシームレスに統合し、Citrix NetScaler SSL VPN のログインとポータル アクセスのセキュリティを強化します。 これは LDAP または RADIUS プロトコルを利用して実行できます。 次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

 構成ガイド| 説明
------------- | ------------- |
 [LDAP の Citrix NetScaler の SSL VPN と Azure MFA の構成](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx)| Citrix NetScaler SSL VPN と Azure MFA アプライアンスを LDAP でシームレスに統合します。|
 [RADIUS の Citrix NetScaler の SSL VPN と Azure MFA の構成](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx)| Citrix NetScaler SSL VPN アプライアンスと Azure MFA を RADIUS でシームレスに統合します。

## Juniper/Pulse Secure SSL VPN アプライアンスと Azure Multi-Factor Authentication

Azure Multi-Factor Authentication は Juniper/Pulse Secure SSL VPN アプライアンスとシームレスに統合し、Juniper/Pulse Secure SSL VPN のログインとポータル アクセスのセキュリティを強化します。 これは LDAP または RADIUS プロトコルを利用して実行できます。 次のいずれかを選択し、詳しい段階的構成ガイドをダウンロードしてください。

 構成ガイド| 説明
------------- | ------------- |
 [LDAP の juniper Pulse/セキュリティで保護された SSL VPN と Azure MFA の構成](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Juniper/Pulse Secure SSL VPN と Azure MFA アプライアンスを LDAP でシームレスに統合します。|
 [RADIUS の juniper Pulse/セキュリティで保護された SSL VPN と Azure MFA の構成](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx)| Juniper/Pulse Secure SSL VPN アプライアンスと Azure MFA を RADIUS でシームレスに統合します。





