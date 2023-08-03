# Timeline

The following is a timeline map of the events fired in the CMS (frontend).

1. [cms.page.beforeDisplay](/docs/v1.2/api/events/cms/page/beforeDisplay)
1. [cms.page.initComponents](/docs/v1.2/api/events/cms/page/initComponents)
    - `layout::onInit()`
    - `page::onInit()`
1. [cms.page.init](/docs/v1.2/api/events/cms/page/init)
1. [cms.ajax.beforeRunHandler](/docs/v1.2/api/events/cms/ajax/beforeRunHandler)
1. [cms.component.beforeRunAjaxHandler](/docs/v1.2/api/events/cms/component/beforeRunAjaxHandler)
1. [cms.component.runAjaxHandler](/docs/v1.2/api/events/cms/component/runAjaxHandler)
1. [cms.page.start](/docs/v1.2/api/events/cms/page/start) -- *start of page lifecycle*
    - `layout::onStart()`
    - `layout::runComponents()`
        - **component.beforeRun**
        - `component::onRun()`
        - **component.run**
    - `layout::onBeforePageStart()`
    - `page::onStart()`
    - `page::runComponents()`
        - **component.beforeRun**
        - `component::onRun()`
        - **component.run**
    - `page::onEnd()`
    - `layout::onEnd()`
1. [cms.page.end](/docs/v1.2/api/events/cms/page/end) -- *end of page lifecycle*
1. [cms.page.beforeRenderPage](/docs/v1.2/api/events/cms/page/beforeRenderPage)
    - [cms.page.beforeRenderPartial](/docs/v1.2/api/events/cms/page/beforeRenderPartial)
    - [cms.page.renderPartial](/docs/v1.2/api/events/cms/page/renderPartial)
1. [cms.page.render](/docs/v1.2/api/events/cms/page/render)
1. [cms.page.postprocess](/docs/v1.2/api/events/cms/page/postprocess)
1. [cms.page.display](/docs/v1.2/api/events/cms/page/display)

> *NOTE:* Adding the following code in your Plugin's `boot()` method will dump the CMS page event stack to your `system.log` file:

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
