---
layout: post
title: Bug 1110624 - How does an iframe start on B2G
---

On B2G, an app is just an embedded iframe like this: `<iframe src=... remote mozbrowser mozapp=...>`. It is mysterious to me how does that HTML tag become a process and load the content. Because of bug 1110624, I had a chance to look into the sequence, and figured out how is that happened. Some classes are involved to load the things according to the special attributes. I drew a simplified classes diagram:

![classes](/public/images/bug-1110624.png)

I don't know how to express the sequence simply, so this's like a stack and the indent (1 space) indicates the stack level.

<pre>
iframe.src = ...
 HTMLIFrameElement::SetAttr()
  nsElementFrameLoaderOwner::LoadSrc()
   nsElementFrameLoaderOwner::EnsureFrameLoader()
    mFrameLoader = nsFrameLoader::Create()
  nsFrameLoader::LoadFrame()
   nsFrameLoader::LoadURI()
    nsDocument::InitializeFrameLoader()
     nsDocument::MaybeInitializeFinalizeFrameLoaders()
      nsFrameLoader::ReallyStartLoading()
       nsFrameLoader::ReallyStartLoadingInternal()
        if (mRemoteFrame) {
         mRemoteBrowser = ContentParent::CreateBrowserOrApp()
          if (isBrowser) {
           ContentParent::GetNewOrUsedBrowserProcess()
          } else {
           ContentParent::GetNewOrPreallocatedAppProcess()
          }
          ContentParent::AllocateTabId()
          new TabParent()
          ContentParent::SendPBrowserConstructor()
          --- IPC ---
          ContentChild::AllocPBrowserChild()
           nsIContentChild::AllocPBrowserChild()
            TabChild::Create()
             if (sPreallocatedTab) {
              sPreallocatedTab->SetTabId()
              sPreallocatedTab->SetTabContext()
             } else {
              new TabChild()
             }
           ContentChild::RecvPBrowserConstructor()
        nsFrameLoader::ShowRemoteFrame()
         TabParent::Show()
          PBrowserParent::SendShow()
          --- IPC ---
          TabChild::RecvShow()
           TabChild::InitTabChildGlobal()
            TabChild::RecvLoadRemoteScript("BrowserElementChild.js")
        NotifyObservers("remote-browser-shown")
         nsBrowserElement::InitBrowserElementAPI()
          mBrowserElementAPI = do_CreateInstance("BrowserElementParent.js")
          mBrowserElementAPI->SetFrameLoader()
        mRemoteBrowser->LoadURL()
         TabParent::LoadURL()
         PBrowserParent::SendLoadURL()
         --- IPC ---
         TabChild::RecvLoadURL()
          nsIWebNavigation::LoadURI()
           nsDocShell::LoadURI()
        } else { // !mRemoteFrame
         nsDocShell::LoadURI()
        }
</pre>
