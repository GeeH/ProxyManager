---
title: Tuning the ProxyManager for production
---

## Tuning the ProxyManager for production

By default, all proxy factories generate the required proxy classes at runtime.

Proxy generation causes I/O operations and uses a lot of reflection, so be sure to have
generated all of your proxies **before deploying your code on a live system**, or you
may experience poor performance.

To generate proxies and store them as files, you need to use the `FileWriterGeneratorStrategy` 
by configuring ProxyManager.
The files generated in the directory will be needed to autoload them.

You can configure ProxyManager so that it will try autoloading the proxies first.
Generating them "bulk" is not yet implemented:

```php
    
$config = new \ProxyManager\Configuration();

// generate the proxies and store them as files
$fileLocator = new \ProxyManager\FileLocator\FileLocator(__DIR__.'/my/generated/classes/cache/dir');
$config->setGeneratorStrategy(new \ProxyManager\GeneratorStrategy\FileWriterGeneratorStrategy($fileLocator));

// set the directory to read the generated proxies from
$config->setProxiesTargetDir(__DIR__ . '/my/generated/classes/cache/dir');

// then register the autoloader
spl_autoload_register($config->getProxyAutoloader());

// pass the configuration to proxymanager factory
$factory = new ProxyManager\Factory\LazyLoadingValueHolderFactory($config);

```
Generating a classmap with all your proxy classes in it will also work perfectly.

Please note that all the currently implemented `ProxyManager\Factory\*` classes accept
a `ProxyManager\Configuration` object as optional constructor parameter. This allows for
fine-tuning of ProxyManager according to your needs.
