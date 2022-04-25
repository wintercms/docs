## Timeline map of the events fired in the CMS (frontend)

1. [cms.page.beforeDisplay](../events/event/cms.page.beforeDisplay)

1. [cms.page.initComponents](../events/event/cms.page.initComponents)

  `layout::onInit()`<br>
  `page::onInit()`
   
1. [cms.page.init](../events/event/cms.page.init)

1. [cms.ajax.beforeRunHandler](../events/event/cms.ajax.beforeRunHandler)

1. [cms.component.beforeRunAjaxHandler](../events/event/cms.component.beforeRunAjaxHandler)

1. [cms.component.runAjaxHandler](../events/event/cms.component.runAjaxHandler)

1. [cms.page.start](../events/event/cms.page.start) -- *start of page lifecycle*

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

1. [cms.page.end](../events/event/cms.page.end) -- *end of page lifecycle*

1. [cms.page.beforeRenderPage](../events/event/cms.page.beforeRenderPage)

1. [cms.page.render](../events/event/cms.page.render)

1. [cms.page.postprocess](../events/event/cms.page.postprocess)

1. [cms.page.display](../events/event/cms.page.display)
