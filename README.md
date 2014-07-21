URLLoaderCache
==============

It is a queue and cache with url loader just like 'spark.core.ContentCache' from Flex 4.5

Problem is ContentCache works only with Loader, so you can't use it for text or bin data loading. Loader provide image files only.

This is just copy of ContentCache with few changes: I'm using URLLoader, and ContentRequest.content points to URLLoader. Usage is same with BitmapImage class.
