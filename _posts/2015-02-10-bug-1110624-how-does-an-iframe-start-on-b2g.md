---
layout: post
title: 
---

`<iframe remote mozbrowser mozapp=...>`

!(/public/images/bug-1110624.png)

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
                    nsFrameLoader::ShowRemoteFrame()
                      TabParent::Show()
                        PBrowserParent::SendShow()
                        --- IPC ---
                        TabChild::RecvShow()
                          TabChild::InitTabChildGlobal()
                            TabChild::RecvLoadRemoteScript("BrowserElementChild.js")
                      InitializeBrowserAPI()
                        nsBrowserElement::InitBrowserElementAPI()
                          mBrowserElementAPI = do_CreateInstance("BrowserElementParent.js")
                          mBrowserElementAPI->SetFrameLoader()
                      NotifyObservers("remote-browser-shown")
                    mRemoteBrowser->LoadURL()
                      TabParent::LoadURL()
                        PBrowserParent::SendLoadURL()
                        --- IPC ---
                        TabChild::RecvLoadURL()
                          nsIWebNavigation::LoadURI()
                            nsDocShell::LoadURI()
                  } else {
                    nsDocShell::LoadURI()
                  }