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

	+ [cms.page.beforeRenderPartial](../events/event/cms.page.beforeRenderPartial)
	+ [cms.page.renderPartial](../events/event/cms.page.renderPartial)

1. [cms.page.render](../events/event/cms.page.render)

1. [cms.page.postprocess](../events/event/cms.page.postprocess)

1. [cms.page.display](../events/event/cms.page.display)

> *Note:* Adding the following code in your Plugin's `boot()` method will dump the CMS page event stack to your `system.log` file:

```
$events_history = [];
Event::listen('*', function ($event, $params) use (&$events_history) {
    if (!str_starts_with($event, 'cms.')) {
        return;
    }
    $events_history[] = $event;
    if ($event === 'cms.page.display') {
        trace_log($events_history);
    }
});
```
