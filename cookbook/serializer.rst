.. index::
   single: Serializer

Usare il Serializer
===================

Serializzare e deserializzare da e verso oggetti nei vari formati (p.e.
JSON o XML) è un argomento davvero complesso. Symfony dispone di un
:doc:`componente Serializer </components/serializer>`, che fornisce alcuni
strumenti che possono essere sfruttati per una soluzione.

In pratica, prima di iniziare, familiarizzare con serializzatori, normalizzatori
e codificatori, leggendo la documentazione del :doc:`componente Serializer </components/serializer>`.
Si dovrebbe altresì controllare il bundle `JMSSerializerBundle`_, che espande le
funzionalità offerte dal serializer di base di Symfony.

Attivare il Serializer
----------------------

.. versionadded:: 2.3
    Serializer è sempre stato in Symfony, ma prima di Symfony 2.3,
    bisognava costruirsi il servizio ``serializer`` da soli.

Il servizio ``serializer`` non è disponibile in modo predefinito. Per abilitarlo, occorre attivarlo
nella configurazione:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        framework:
            # ...
            serializer:
                enabled: true

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <framework:config ...>
            <!-- ... -->
            <framework:serializer enabled="true" />
        </framework:config>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('framework', array(
            // ...
            'serializer' => array(
                'enabled' => true
            ),
        ));

Aggiungere normalizzatori e codificatori
----------------------------------------

Una volta attivato, il servizio ``serializer`` sarà disponibile dentro il contenitore
e sarà caricato con due :ref:`codificatori<component-serializer-encoders>`
(:class:`Symfony\\Component\\Serializer\\Encoder\\JsonEncoder` e
:class:`Symfony\\Component\\Serializer\\Encoder\\XmlEncoder`),
ma nessun :ref:`normalizzatore<component-serializer-normalizers>`, quindi bisognerà
caricarne uno proprio.

Si possono caricare normalizzatori e/o codificatori, aggiungeno i tag
:ref:`serializer.normalizer<reference-dic-tags-serializer-normalizer>` e
:ref:`serializer.encoder<reference-dic-tags-serializer-encoder>`. È altresì
possibile impostare una priorità del tag, per decidere l'ordine di abbinamento.

Ecco un esempio su come caricare
la classe :class:`Symfony\\Component\\Serializer\\Normalizer\\GetSetMethodNormalizer`:

.. configuration-block::

    .. code-block:: yaml

       # app/config/config.yml
       services:
          get_set_method_normalizer:
             class: Symfony\Component\Serializer\Normalizer\GetSetMethodNormalizer
             tags:
                - { name: serializer.normalizer }

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <services>
            <service id="get_set_method_normalizer" class="Symfony\Component\Serializer\Normalizer\GetSetMethodNormalizer">
                <tag name="serializer.normalizer" />
            </service>
        </services>

    .. code-block:: php

        // app/config/config.php
        use Symfony\Component\DependencyInjection\Definition;

        $definition = new Definition(
            'Symfony\Component\Serializer\Normalizer\GetSetMethodNormalizer'
        ));
        $definition->addTag('serializer.normalizer');
        $container->setDefinition('get_set_method_normalizer', $definition);

.. note::

    La classe :class:`Symfony\\Component\\Serializer\\Normalizer\\GetSetMethodNormalizer`
    non funziona di proposito. Appena si ha un grafo di oggetti circolare, viene
    creato un loop infinito quando si chiamano i getter. Questo vuole essere un incoraggiamento
    ad aggiungere i propri normalizzatori, che siano adatti ai propri casi d'uso.

.. _JMSSerializerBundle: http://jmsyst.com/bundles/JMSSerializerBundle
