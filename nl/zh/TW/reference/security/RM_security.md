---

copyright:
  years: 2016, 2018
lastupdated: "2018-07-03"

---

{:new_window: target="\_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}
{:pre: .pre}

# 風險與安全管理
{: #RM_security}

您可以加強安全，以建立、強制施行裝置連線安全並產生報告。利用此加強安全，除了 {{site.data.keyword.iot_short_notm}} 用來決定裝置與平台連接方式和位置的使用者 ID 和記號，還會使用憑證和傳輸層安全 (TLS) 鑑別。

## 憑證撤銷清冊及用戶端憑證
{: #CRLs}

使用用戶端憑證來鑑別裝置的配置支援「憑證撤銷清冊 (CRL)」。指定為 CRL 配送點的位置必須支援 HTTP 或 HTTPS 存取，並且可以聯繫網站。如果無法到達 CRL 的配送點，或 CRL 無效，或者在配送點找不到 CRL，則會拒絕用戶端連線。 
 

用戶端憑證必須透過「X509v3 延伸規格」的「X509v3 金鑰用法」區段中指定「憑證簽署」的憑證管理中心 (CA) 憑證予以簽署。若要使用 CRL 撤銷用戶端憑證，簽署用戶端憑證的 CA 憑證也必須在「X509v3 延伸規格」的「X509v3 金鑰用法」區段中包括「CRL 簽署」。此配置容許 CA 憑證在必要時發出及簽署 CRL 更新項目。用戶端憑證配送點中指定的 CRL 只能用來判斷是否已撤銷憑證。

請確定您已測試憑證及 CRL，尤其是您產生自己的憑證及 CRL 而不是使用來自已辨識憑證管理中心的憑證時。


## 配置憑證
{: #certificates}

當憑證啟用時，在裝置與伺服器通訊期間，沒有有效憑證（配置於安全設定中）的任何裝置都會被拒絕存取，即使其使用有效的使用者 ID 和密碼也一樣。

為了配置裝置的憑證和伺服器存取權，系統操作員會在 Watson IoT Platform 平台中登錄相關聯的憑證管理中心 (CA) 憑證，並選擇性地登錄訊息伺服器憑證。
為了配置裝置的用戶端憑證和伺服器存取權，系統操作員會將關聯的憑證管理中心 (CA) 憑證和傳訊伺服器憑證匯入 {{site.data.keyword.iot_short_notm}}。安全分析師接著會配置裝置與平台之間連線的預設安全原則。分析師可以針對不同的裝置類型，新增不同的原則。

如需如何配置憑證的相關資訊，請參閱[配置憑證](set_up_certificates.html)。

## 安全設定
{: #connect_policy}

安全設定（包括使用連線原則設定、CA 憑證及傳訊伺服器憑證）會強制執行裝置如何連接至平台。您可以針對所有裝置類型來設定預設連線原則，並可針對特定裝置類型來自訂設定。原則可以設為容許未加密的連線，以強制施行傳輸層安全 (TLS) 連線，並且讓裝置能夠以用戶端憑證來鑑別。

如需如何配置連線安全原則的相關資訊，請參閱[配置安全原則](set_up_policies.html)。

黑名單及白名單原則能夠控制允許哪些位置的裝置連接至組織的帳戶。黑名單會識別可拒絕其存取伺服器的所有 IP 位址、CIDR 或國家/地區。白名單可提供特定 IP 位址的明確存取權。

如需如何配置黑名單及白名單原則的相關資訊，請參閱[配置黑名單及白名單](set_up_policies.html#config_black_white)。

## 組織方案及安全原則
加強的安全原則可讓組織使用連線原則及黑名單和白名單原則，來決定裝置如何連接平台，以及向平台鑑別。組織可用的安全原則選項，取決於組織的方案類型：

**標準方案：**
- 系統操作員可以使用下列選項來配置連線原則：
    - 選用的傳輸層安全 (TLS)
    - 使用記號鑑別的傳輸層安全 (TLS)
    - 使用記號和用戶端憑證鑑別的傳輸層安全 (TLS)

**進階安全方案 (ASP) 或精簡方案：**
- 系統操作員可以使用下列選項來配置連線原則：
    - 選用的傳輸層安全 (TLS)
    - 使用記號鑑別的傳輸層安全 (TLS)
    - 使用用戶端憑證鑑別的傳輸層安全 (TLS)
    - 使用記號和用戶端憑證鑑別的傳輸層安全 (TLS)
    - 使用用戶端憑證或記號的傳輸層安全 (TLS)
- 系統操作員可以配置黑名單或白名單

## 風險與安全管理儀表板及報告
{: #dashboard}

系統操作員和安全分析師可以使用「風險與安全管理」儀表板來檢視整體安全狀態。儀表板上的卡片可以提供綜合性規範概觀，以及裝置的連線狀態。

下列儀表板卡片適用於分析風險及規範：
 - **連線安全規範** - 顯示連接至伺服器的裝置規範層次。
 - **黑名單/白名單規範** - 根據作用中黑名單或白名單原則，顯示裝置規範層次。
 - **整體原則規範**（測試版）- 根據所有現有原則，顯示整體規範層次。
 - **原則違規**（測試版）- 顯示每一個原則的整體違規。

**重要事項**：**整體原則規範**及**原則違規**卡片僅是有限「測試版」程式的一部分。若要啟用這些儀表板卡片，請開啟**設定**頁面上的**實驗性特性**。

### 往下探查原則報告（測試版）
{: #drill_down}

**重要事項**：「風險管理」原則的往下探查報告特性僅是有限「測試版」程式的一部分。若要啟用往下探查報告，請開啟**設定**頁面上的**實驗性特性**。

系統操作員可以往下探查每一個報告，以檢視符合或違反原則之裝置的特定詳細資料，這是「測試版」特性的一部分。

您可以從列出所有安全原則的**原則**頁面以及每一個原則的詳細資料頁面中，以從儀表板卡片中存取往下探查報告。您可以往下探查至報告中的三個詳細程度：
1. 第一個層次列出所選取原則類型中包含的所有原則，例如，預設連線原則及任何自訂連線原則。
2. 第二個層次顯示違反原則的裝置及違規原因。
3. 第三個層次顯示違反原則之個別裝置的特定詳細資料。
