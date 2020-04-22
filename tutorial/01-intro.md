<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3e7c8-101">In diesem Lernprogramm erfahren Sie, wie Sie eine Reaktionssystem eigene APP erstellen, die die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-101">This tutorial teaches you how to build an React Native app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="3e7c8-102">Wenn Sie das fertige Lernprogramm einfach herunterladen möchten, können Sie das GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-ios-swift)herunterladen oder Klonen.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-102">If you prefer to just download the completed tutorial, you can download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-ios-swift).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e7c8-103">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3e7c8-103">Prerequisites</span></span>

<span data-ttu-id="3e7c8-104">Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie Folgendes auf Ihrem Entwicklungscomputer installiert haben.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-104">Before you start this tutorial, you should have the following installed on your development machine.</span></span>

- [<span data-ttu-id="3e7c8-105">Xcode</span><span class="sxs-lookup"><span data-stu-id="3e7c8-105">Xcode</span></span>](https://developer.apple.com/xcode/)
- [<span data-ttu-id="3e7c8-106">CocoaPods</span><span class="sxs-lookup"><span data-stu-id="3e7c8-106">CocoaPods</span></span>](https://cocoapods.org)

<span data-ttu-id="3e7c8-107">Sie sollten auch über ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Arbeits-oder Schulkonto verfügen.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-107">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="3e7c8-108">Wenn Sie kein Microsoft-Konto haben, gibt es mehrere Optionen, um ein kostenloses Konto zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="3e7c8-108">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="3e7c8-109">Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="3e7c8-109">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="3e7c8-110">Sie können sich [für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-110">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="3e7c8-111">Dieses Tutorial wurde mit Xcode Version 11,4 und CocoaPods Version 1.9.1 geschrieben die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-111">This tutorial was written using Xcode version 11.4 and CocoaPods version 1.9.1 The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="3e7c8-112">Feedback</span><span class="sxs-lookup"><span data-stu-id="3e7c8-112">Feedback</span></span>

<span data-ttu-id="3e7c8-113">Geben Sie Feedback zu diesem Lernprogramm im [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-ios-swift)an.</span><span class="sxs-lookup"><span data-stu-id="3e7c8-113">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-ios-swift).</span></span>
