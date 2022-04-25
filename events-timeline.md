## Timeline map of the events fired in the CMS (frontend)

1. **cms.page.beforeDisplay**

1. **cms.page.initComponents**

  `layout::onInit()`<br>
  `page::onInit()`
   
1. **cms.page.init**

1. **cms.ajax.beforeRunHandler**

1. **cms.component.beforeRunAjaxHandler**

1. **cms.component.runAjaxHandler**

1. **cms.page.start** -- *start of page lifecycle*

  `layout::onStart()`

  `layout::runComponents()`
    *  **component.beforeRun**<br>
      `component::onRun()`
    *  **component.run**

  `layout::onBeforePageStart()`
  
  `page::onStart()`
  `page::runComponents()`
    * **component.beforeRun**<br>
      `component::onRun()`
    * **component.run**
  `page::onEnd()`

  `layout::onEnd()`

1. **cms.page.end** -- *end of page lifecycle*

1. **cms.page.beforeRenderPage**

1. **cms.page.render**

1. **cms.page.postprocess**

1. **cms.page.display**
