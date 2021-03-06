<properties
	pageTitle="如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器 | Microsoft Azure"
	description="涵蓋如何執行無訊息安裝 Azure AD 應用程式 Proxy 連接器，為內部部署的應用程式提供安全的遠端存取。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="kgremban"/>

# 如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器

您想要能傳送安裝指令碼至多部 Windows 伺服器，或傳送至未啟用使用者介面的 Windows Server。本主題說明如何建立 Windows PowerShell 指令碼來啟用自動安裝，以安裝並註冊您的 Azure AD 應用程式 Proxy 連接器。

## 啟用存取
應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為連接器的精簡型 Windows Server 服務。應用程式 Proxy 連接器必須使用全域系統管理員和密碼向 Azure AD 目錄註冊後才能運作。通常，這是在連接器安裝期間於一個快顯對話方塊中輸入的。此外，您也可以使用 Windows PowerShell 來建立認證物件以輸入您的註冊資訊，或者您可以建立自己的語彙基元並使用它來輸入註冊資訊。

## 步驟 1：安裝連接器，但不註冊


下列是安裝連接器但不註冊連接器的方式：


1. 開啟命令提示字元。
2. 執行下列命令，其中的 /q 表示無訊息安裝，即安裝不會提示您接受「使用者授權合約」。

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## 步驟 2：向 Azure Active Directory 註冊連接器
這可以使用下列其中一種方法來完成：


- 使用 Windows PowerShell 認證物件註冊連接器
- 使用離線時建立的語彙基元註冊連接器

### 使用 Windows PowerShell 認證物件註冊連接器


1. 執行下列命令以建立 Windows PowerShell 認證物件，其中的 "<username>" 和 "<password>" 應取代為您的目錄的使用者名稱和密碼：

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. 移至 **C:\\Program Files\\Microsoft AAD App Proxy Connector**，並使用您建立的 PowerShell 認證物件執行指令碼，其中 $cred 是您所建立之 PowerShell 認證物件的名稱：

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### 使用離線時建立的語彙基元註冊連接器

1. 使用程式碼片段中的值，建立使用 AuthenticationContext 類別的離線語彙基元：


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. 建立語彙基元後，請使用該語彙基元建立一個 SecureString：<br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. 執行下列 Windows PowerShell 命令，其中 SecureToken 是您在上面的步驟中所建立之語彙基元的名稱，而 tenantID 則是您租用戶的 GUID：<br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## 另請參閱

- [啟用 Azure Active Directory 的應用程式 Proxy](active-directory-application-proxy-enable.md)
- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

如需最新消息，請查閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)

<!---HONumber=AcomDC_0622_2016-->