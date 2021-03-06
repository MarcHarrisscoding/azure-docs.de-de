---
title: Datenanforderungen von Azure AD SSPR | Microsoft-Dokumentation
description: Datenanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung und deren Bereitstellung
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 790ca2ccb2d365876e15ca57e1aa199ac519fd73
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2018
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung

Damit die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) bereitgestellt werden kann, müssen Authentifizierungsdaten vorhanden sein. Einige Organisationen überlassen es ihren Benutzern, dass diese ihre Authentifizierungsdaten selbst eingeben. Aber viele Organisationen bevorzugen ein Synchronisieren mit Daten, die bereits in Active Directory vorhanden ist. Die synchronisierten Daten werden für Azure AD und SSPR verfügbar gemacht, ohne dass ein Eingreifen eines Benutzers erforderlich ist, wenn Sie:
   * Die Daten ordnungsgemäß in Ihrem lokalen Verzeichnis formatieren.
   * [Azure AD Connect durch Verwenden der Express-Einstellungen](./../connect/active-directory-aadconnect-get-started-express.md) konfigurieren.

Für ein ordnungsgemäßes Funktionieren müssen Telefonnummern im Format *+Landesvorwahl Telefonnummer* vorliegen (Beispiel: +1 4255551234).

> [!NOTE]
> Zwischen Landesvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Für die Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format +1 4255551234X12345 werden Nebenstellen vor dem Anruf entfernt.

## <a name="fields-populated"></a>Ausgefüllte Felder

Wenn Sie die Standardeinstellungen in Azure AD Connect verwenden, werden die folgenden Zuordnungen vorgenommen:

| Lokales Active Directory | Azure AD | Kontaktinformationen für die Azure AD-Authentifizierung |
| --- | --- | --- |
| telephoneNumber | Bürotelefon | Alternatives Telefon |
| mobile | Mobiltelefon | Phone |

Diese Felder werden möglicherweise leer angezeigt, bis ein Benutzer seine Authentifizierungsdaten bestätigt.

Ein globaler Administrator kann die Kontaktinformationen des Benutzers für die Authentifizierung manuell festlegen, wie im folgenden Screenshot gezeigt.

![Kontakt][Contact]

## <a name="security-questions-and-answers"></a>Sicherheitsfragen und -antworten

Die Sicherheitsfragen und -antworten werden sicher in Ihrem Azure AD-Mandanten gespeichert und sind für Benutzer nur über das [SSPR-Registrierungsportal](https://aka.ms/ssprsetup) zugänglich. Administratoren können den Inhalt der Fragen und Antworten anderer Benutzer weder anzeigen noch ändern.

### <a name="what-happens-when-a-user-registers"></a>Was geschieht bei Registrierung eines Benutzers?

Wenn sich ein Benutzer registriert, zeigt die Registrierungsseite die folgenden Felder an:

* **Telefon für Authentifizierung**
* **E-Mail für Authentifizierung**
* **Sicherheitsfragen und Antworten**

Wenn Sie einen Wert für **Mobiltelefon** oder **Alternative E-Mail-Adresse** angegeben haben, können Benutzer diese Werte sofort verwenden, um ihre Kennwörter zurücksetzen, selbst wenn sie sich nicht für den Dienst registriert haben. Benutzer sehen diese Werte außerdem, wenn sie sich erstmalig registrieren, und sie können die Werte bei Bedarf ändern. Nachdem sich Benutzer erfolgreich registriert haben, werden diese Werte in den Feldern **Telefon für Authentifizierung** und **E-Mail für Authentifizierung** beibehalten.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Festlegen und Lesen der Authentifizierungsdaten über PowerShell

Die folgenden Felder können über PowerShell festgelegt werden:

* **Alternative E-Mail-Adresse**
* **Mobiltelefon**
* **Bürotelefon**: Kann nur festgelegt werden, wenn Sie keine Synchronisierung mit einem lokalen Verzeichnis vornehmen

### <a name="use-powershell-version-1"></a>Verwenden von PowerShell Version 1

Zunächst müssen Sie [das Azure AD PowerShell-Modul herunterladen und installieren](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Nachdem Sie es installiert haben, können Sie anhand der folgenden Schritte die einzelnen Felder konfigurieren.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Lesen der Authentifizierungsdaten mit PowerShell Version 1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Lesen der Optionen „Telefon für Authentifizierung“ und „E-Mail für Authentifizierung“

Wenn Sie PowerShell Version 1 verwenden und die Optionen **Telefon für Authentifizierung** und **E-Mail für Authentifizierung** lesen möchten, führen Sie die folgenden Befehle aus:

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Verwenden von PowerShell Version 2

Zunächst müssen Sie [das Azure AD Version 2 PowerShell-Modul herunterladen und installieren](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Nachdem Sie es installiert haben, können Sie anhand der folgenden Schritte die einzelnen Felder konfigurieren.

Um schnell aus neueren Versionen von PowerShell zu installieren, die „Install-Module“ unterstützen, führen Sie die folgenden Befehle aus. (In der ersten Zeile wird überprüft, ob das Modul bereits installiert ist.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Festlegen der Authentifizierungsdaten mit PowerShell Version 2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Lesen der Authentifizierungsdaten mit PowerShell Version 2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Nächste Schritte

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Globale Administratoren können die Kontaktinformationen eines Benutzer für die Authentifizierung bearbeiten"
