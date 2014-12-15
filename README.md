URLLoaderCache
==============

It is a queue and cache with url loader just like 'spark.core.ContentCache' from Flex 4.5

Problem is ContentCache works only with Loader, so you can't use it for text or bin data loading. Loader provide image files only.

This is just copy of ContentCache with few changes: I'm using URLLoader, and ContentRequest.content points to URLLoader. Usage is same with BitmapImage class.

#### Simple example
```
var contentRequest:ContentRequest = contentLoader.load(source);
if (contentRequest.complete) {
	//todo you can use contentRequest.content
} else {
    contentRequest.addEventListener(Event.COMPLETE,
            content_completeHandler);
}
```

#### Hard example
Here we will consider a class that uses URLLoaderCache to load files.
To take full example you can take a look at: [https://github.com/kelegorm/AS3SVGRenderer/blob/master/SVGRendererFlex/src/com/lorentz/SVG/Flex/SVG.as] or you can consider Image and BitmapImage spark classes.

```
//somewhere
public static const somethingContent:URLLoaderCahce = new URLLoaderCache();

//using component
var something:SomethingLoader = new SomethingLoader();
something.contentLoader = somethingContent;
something.source = 'my-super-file.any';


//write loader class
public class SomethingLoader {
    private var _contentLoader:IContentLoader;
    /**
     *  Optional custom url loader (e.g. cache or queue) to
     *  associate with content loader client.
     *
     *  Don't use for it ContentCache, it works with images only. Use 'https://github.com/kelegorm/URLLoaderCache'.
     *
     *  @default null
     */
    public function get contentLoader():IContentLoader {
        return _contentLoader;
    }

    public function set contentLoader(value:IContentLoader):void {
        if (value != _contentLoader) {
            _contentLoader = value;
        }
    }

    private var _source:Object;
    [Bindable]
    public function get source():Object {
        return _source;
    }
    public function set source(value:Object):void {
        _source = value;
        _sourceInvalid = true;
        applySource();
    }

    private function applySource():void {
        if ((_source is String || _source is URLRequest) {
            loadExternal(_source);
        } else {
            ...
        }
    }

    private function loadExternal(source:Object):void {
        if (contentLoader) {
            var contentRequest:ContentRequest = contentLoader.load(source);
            if (contentRequest.complete) {
                //todo PROFIT!
            } else {
                loadingContent = contentRequest;
                attachLoadingListeners();
            }
        } else {
            //todo load in another way
        }
    }

    private function attachLoadingListeners():void {
        if (loadingContent) {
            loadingContent.addEventListener(Event.COMPLETE,
                    loader_completeHandler, false, 0, true);
            loadingContent.addEventListener(IOErrorEvent.IO_ERROR,
                    loader_ioErrorHandler, false, 0, true);
            loadingContent.addEventListener(ProgressEvent.PROGRESS,
                    loader_progressHandler, false, 0, true);
            loadingContent.addEventListener(SecurityErrorEvent.SECURITY_ERROR,
                    loader_securityErrorHandler, false, 0, true);
            loadingContent.addEventListener(HTTPStatusEvent.HTTP_STATUS,
                    dispatchEvent, false, 0, true);
        }
    }

    private function loader_progressHandler(event:ProgressEvent):void {
        // nothing
    }

    private function loader_securityErrorHandler(event:SecurityErrorEvent):void {
        clearLoadingContent();
    }

    private function loader_ioErrorHandler(event:IOErrorEvent):void {
        clearLoadingContent();
    }

    private function loader_completeHandler(event:Event):void {
        var loader:URLLoader = event.target.content as URLLoader;

        if (loader) {
            //todo PROFIT!!
        }

        dispatchEvent(event);

        // Remove any event listeners from load-event dispatcher.
        clearLoadingContent();
    }

    private function clearLoadingContent():void {
        removeLoadingListeners();
        loadingContent = null;
    }
}
```

