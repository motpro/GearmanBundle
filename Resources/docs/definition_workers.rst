Definition of Workers
=====================

This Bundle allows you to configure whatever as a Job. It provides you an easy
way to execute it with Supervisor, for example. Moreover, it let you call client
methods in Symfony2 environment in a really simple and practical way.
Job annotations always overwrite work annotations, and work annotations always
overwrite environment settings.

.. code-block:: php

    <?php

    namespace Acme\AcmeBundle\Workers;

    use Mmoreram\GearmanBundle\Driver\Gearman;

    /**
     * @Gearman\Work(
     *     iterations = 3,
     *     description = "Worker test description",
     *     defaultMethod = "doBackground",
     *     servers = {
     *         { "host": "192.168.1.1", "port": 4560 },
     *         { "host": "192.168.1.2", "port": 4560 },
     *     }
     * )
     */
    class AcmeWorker
    {
        /**
         * Test method to run as a job
         *
         * @param \GearmanJob $job Object with job parameters
         *
         * @return boolean
         *
         * @Gearman\Job(
         *     iterations = 3,
         *     name = "test",
         *     description = "This is a description"
         * )
         */
        public function testA(\GearmanJob $job)
        {
            echo 'Job testA done!' . PHP_EOL;

            return true;
        }

        /**
         * Test method to run as a job
         *
         * @param \GearmanJob $job Object with job parameters
         *
         * @return boolean
         *
         * @Gearman\Job(
         *     defaultMethod = "doLowBackground"
         * )
         */
        public function testB(\GearmanJob $job)
        {
            echo 'Job testB done!' . PHP_EOL;

            return true;
        }
    }

Worker annotations
~~~~~~~~~~~~~~~~~~

.. code-block:: php

    /**
     * @Gearman\Work(
     *     name = "MyAcmeWorker",
     *     iterations = 3,
     *     description = "Acme Worker. Containing multiple available jobs",
     *     defaultMethod = "doHigh",
     *     servers = {
     *         { "host": "192.168.1.1", "port": 4560 },
     *         { "host": "192.168.1.2", "port": 4560 },
     *     }
     * )
     */

- name : Name of work. You can associate a group of jobs with some keyword
- description : Short description about all jobs inside
- iterations : You can overwrite iterations of all jobs inside
- servers : array containing servers providers will connect to offer all jobs
- service : You can use even a service. Must specify callable service name
- defaultMethod : You can define witch method will be used as default in all
  jobs

Job annotations
~~~~~~~~~~~~~~~

.. code-block:: php

    /**
     * @Gearman\Job(
     *     name = "doSomething",
     *     iterations = 10,
     *     description = "Acme Job action. This is just a description of a method that do something",
     *     defaultMethod = "doBackground",
     *     servers = { "host": "192.168.1.1", "port": 4560 }
     * )
     */

- name : Name of job. You will use it to call job
- description : Short description about this job. Important field
- iterations : You can overwrite iterations of this job.
- servers : array containing servers providers will connect to offer this job
- defaultMethod : You can define witch method will be used as default in this job

Job as a service
~~~~~~~~~~~~~~~~

If you want to use your service as a worker, you have to specify service
variable in Worker annotation.

.. code-block:: php

    <?php

    namespace Acme\AcmeBundle\Services;

    /**
     * @Gearman\Work(
     *     service="myServiceName"
     * )
     */
    class AcmeService
    {

        /**
         * Test method to run as a job
         *
         * @param \GearmanJob $job Object with job parameters
         *
         * @return boolean
         *
         * @Gearman\Job()
         */
        public function testA(\GearmanJob $job)
        {
            echo 'Job testA done!' . PHP_EOL;

            return true;
        }
    }

And have this service defined in your dependency injection definition file

.. code-block:: yml

    # /Resources/config/services.yml
    bundles:
       Services:
          myServiceName:
             class: Acme\AcmeBundle\Services\AcmeService
             arguments:
                event_dispatcher: @event_dispatcher
                mailer: @mailer