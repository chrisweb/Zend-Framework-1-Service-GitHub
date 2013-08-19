Zend-Framework-1-Service-GitHub
===============================

Very simple Zend Framework 1 Service for GitHub API v3

Usage:
------

IndexController.php

<code>

    public function indexAction()
	{
        
        $bootstrap = $this->getInvokeArg('bootstrap');
        $filescache = $bootstrap->getResource('filescache');
        
        $cachedResults = $filescache->load('github_my_projects');

        if (!$cachedResults) {

            $githubService = new Chrisweb_Service_GitHub();

            $githubService->setPath('/users/chrisweb/repos');

            try {
                $githubResponse = $githubService->query();
            } catch (Exception $exception) {
                $githubResponse = '';
            }
			
			$filescache->save($githubResponse, 'github_my_projects');
                
			$this->view->githubResults = $githubResponse;
            
        } else {
            
            $this->view->githubResults = $cachedResults;
            
        }
	
	}
	
</code>

Bootstrap.php

<code>

    protected function _initFilesCache()
	{

		$filesCacheDirectory = APPLICATION_PATH.'/caches';
        
        if (!is_dir($filesCacheDirectory)) @mkdir($filesCacheDirectory, 0755);

        $cacheFrontendAdapter = $this->configuration->files->cache->frontend->adapter;
        $cacheBackendAdapter = $this->configuration->files->cache->backend->adapter;
        $filesCacheLifetime = $this->configuration->files->cache->lifetime;

        $filesCacheOptions = array(
            'frontend' => array(
                'automatic_serialization' => true,
                'lifetime' => $filesCacheLifetime
            ),
            'backend' => array(
                'cache_dir' => $filesCacheDirectory
            )
        );

        $this->filesCache = Zend_Cache::factory($cacheFrontendAdapter, $cacheBackendAdapter, $filesCacheOptions['frontend'], $filesCacheOptions['backend']);

        return $this->filesCache;

    }
	
</code>
