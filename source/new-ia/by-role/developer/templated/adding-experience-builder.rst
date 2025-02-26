:is-up-to-date: False
:since-version: 4.0.0
:nosearch:

.. index:: Experience Builder, In-Context Editing, ICE

.. _newIa-templated-xb:

======================================
Adding Experience Builder to a Project
======================================

CrafterCMS' Experience Builder (XB) provides a UI layer on top of your applications that enables authors
with in-context editing (ICE) for all the model fields defined in the content types of pages and components.
CrafterCMS developers must integrate their applications with XB, essentially telling XB what field of the
model each element on the view represents. See :ref:`newIa-content-modeling` to learn more about the model.

.. TODO insert <figure: example page with a sample content type side by side showing the relation between page elements
   and content type fields>

If you`re starting from a 3.x ICE system, see :ref:`newIa-upgrading-in-context-editing-templated` for more information
on how to move from the 3.x ICE system to the 4.x Experience Builder (XB) system discussed here.

----------------------------------------------
Creating Experience Builder (XB) Enabled Sites
----------------------------------------------

The concrete integration strategy with XB depends on what kind of application you are developing, however,
overall all you need to do is mark the content element that display CrafterCMS content and initialize XB.
CrafterCMS provides native XB integration for FreeMarker and React applications. Other types of
applications (e.g. Angular, Vue, etc.) can be integrated with XB through the underlying libraries
that power the FreeMarker and React applications. For reference on how to integrate, please see the
sections below for your specific kind of application.

To integrate with XB, all you need to do is:

* Register or mark CrafterCMS content field elements: you tell the system what HTML element represents
  any give model field. Registration can be manual (i.e. invoking specific methods from the XB JavaScript
  libraries), or by putting a specific set of attributes on each tag.
* Initialize XB. Which can also be done manually or by invoking specific methods of the XB JavaScript
  libraries.

Overall, XB's ICE engine works with a coordinate system that you — the developer — use to
tell the CMS which field of the content type each element/component on your page/app maps to.

The coordinate system consists of the following pieces of data:

* **Path**: the path to the content item (e.g. ``/site/components/features/main_feature.xml``)
* **Model ID (a.k.a. object ID, local ID)**: the internal GUID that every content object in CrafterCMS has
  (e.g. ``5a06e244-64f4-4380-8619-1c726fe38e92``)
* **Field ID**: the ID of the field in the content type (e.g. ``heroTitleText_t``)

  * Field IDs may be compound, comprised of the full path to that field when such field is nested within repeat
    groups (e.g. ``carouselSlides_o.slideTitle_t``)

* **Index**: When working with collections (e.g. component selectors or repeat groups), the index of the item within
  it's container collection (e.g. ``0``)

  * Indexes can be compound, comprised of the full path of indexes to that item in the collection (e.g. ``0.1``)

XB's ICE engine requires, at times, what might be considered slightly more verbose markup structure.
In order for the system to be able to direct authors to every piece of the model, as well as allowing
them to edit inline, you need to register each piece of the model as an element on your view.

For example, consider a carousel, where the carousel is modelled as a CrafterCMS component that has
a repeat group field called ``slides_o`` which has two inner fields called ``caption_s`` and ``image_s``.

The markup for a carousel may look like this:

.. code-block:: html

     <div class="carousel">
       <div class="slide">
         <img src="slide1.png" alt="">
         <h2>Slide One</h2>
       </div>
       <div class="slide">
         <img src="slide2.png" alt="">
         <h2>Slide Two</h2>
       </div>
     </div>

In order to register each piece of the model, we would need to introduce a new element.

.. code-block:: html

   <div class="carousel">              <!-- Component (Carousel) -->
     <div>                             <!-- Repeating group (slides_o) — Additional element introduced -->
       <div class="slide">             <!-- Repeat group item (slides_o[0]) -->
         <img src="slide1.png" alt=""> <!-- Repeat group item field (slides_o[0].images_s) -->
         <h2>Slide One</h2>            <!-- Repeat group item field (slides_o[0].caption_s) -->
       </div>
       <div class="slide">             <!-- Repeat group item (slides_o[1]) -->
         <img src="slide2.png" alt=""> <!-- Repeat group item field (slides_o[1].images_s) -->
         <h2>Slide Two</h2>            <!-- Repeat group item field (slides_o[1].caption_s) -->
       </div>
     </div>
   </div>

You can vary exactly where to add this additional element to suit your needs — or those of the libraries
and frameworks that you use to develop your applications. The important aspects are that each field is
represented by an element on the page/app and that the hierarchy of the fields is followed by the
hierarchy of your markup.

Meaning, the component element is the parent of the repeat group element which is a parent of the repeat group items
which are parents of the repeat group item fields, as shown below:

.. code-block:: text

  component
    repeat-group
      item
        item-fields

For example, you could move the additional ``div`` to be the top wrapper, and hence represent the component
instead of the repeat group. Naturally, the repeat group would then be represented by the ``div`` with the
carousel class.

.. code-block:: html

   <div>                    <!-- Component (Carousel) -->
     <div class="carousel"> <!-- Repeating group (slides_o) -->
       ...
     </div>
   </div>

^^^^^^^^^^^
Rules of XB
^^^^^^^^^^^

.. TODO: Find better title?

* The HTML element that is registered with XB as a field must contain only that content, unwrapped.

  * Elements that represent fields of type text, html and other simple values, should print the content
    value directly inside of them without intermediate elements.

      .. list-table::
         :header-rows: 1

         * - Incorrect
           - Correct
         * - .. code-block:: html

                  <!-- Author field (author_s) -->
                  <div class="byline">
                     by ${author_s}
                  </div>
           - .. code-block:: html

                  <div class="byline">
                     by
                     <!-- Author field (author_s) --><span>${author_s}</span>
                  </div>

  * Elements that represent collections (i.e. repeat groups or component collections), must have their
    item elements as direct children.

      .. list-table::
         :header-rows: 1

         * - Incorrect
           - Correct
         * - .. code-block:: html

                  <!-- Component collection field (components_o) -->
                  <div>
                     <div class="column">
                        <!-- Component collection item (components_o) -->
                        <div class="feature>
                           ...
                        </div>
                     </div>
                  </div>
           - .. code-block:: html

                  <!-- Component collection field (components_o) -->
                  <div>
                     <!-- Component collection item (components_o) -->
                     <div class="column">
                        <div class="feature>
                           ...
                        </div>
                     </div>
                  </div>

.. _newIa-xb-freemarker:

^^^^^^^^^^
FreeMarker
^^^^^^^^^^

In FreeMarker applications, in order to integrate with XB, you will use the macros provided by CrafterCMS,
which in turn will set all the right hints (i.e. html attributes) on the markup for
the ICE engine to make things editable to authors.

As mentioned earlier, you need to give XB's ICE engine the *coordinates* to identify each model/field,
so, in addition to their other arguments, each macro receives the following base parameters:

.. _newIa-xbAttributes:

* **Model** (``$model``)

  * By providing the model, internally CrafterCMS extracts the path and model ID (a.k.a object ID)
  * Model is optional since by default it uses the ``contentModel`` FreeMarker context variable for the current template

    * If you need to use a different model, please specify the ``$model`` argument of the macros

  * The HTML attributes for it are ``data-craftercms-model-path`` and ``data-craftercms-model-id``

* **Field ID** (``$field``)

  * The HTML attribute for it is ``data-craftercms-field-id``.

* **Index** (``$index``)

  * The HTML attribute for it is ``data-craftercms-index``.

For example, the following ``div`` element macro

.. code-block:: text

   <@crafter.div $field="columns_o.items_o" $index="0.1">
     ...
   </@crafter.div>

The above will print out to the HTML a ``div`` with all the relevant hints for the ICE engine to pick up
this element as editable. Such ``div`` would look as shown below:

.. code-block:: html
   :linenos:

   <div
     data-craftercms-model-path="/site/website/index.xml"
     data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
     data-craftercms-field-id="columns_o.items_o"
     data-craftercms-index="0.1"
   >...</div>

Start by importing the crafter FreeMarker library on to your FreeMarker template.

.. code-block:: text

   <#import "/templates/system/common/crafter.ftl" as crafter />

Once you've imported ``crafter.ftl``, you can start converting tags to editable elements by switching
each of the tags that represent CrafterCMS content model fields, from plain HTML tags to a macro tag.
Will use the previous carousel example to illustrate.

As seen on the previous section, we introduced an additional element to represent the repeat group
and we ended up with the following markup.

.. code-block:: html
   :linenos:

   <div class="carousel">              <!-- Component (Carousel) -->
     <div>                             <!-- Repeating group (slides_o) — Additional element introduced -->
       <div class="slide">             <!-- Repeat group item (slides_o[0]) -->
         <img src="slide1.png" alt=""> <!-- Repeat group item field (slides_o[0].images_s) -->
         <h2>Slide One</h2>            <!-- Repeat group item field (slides_o[0].caption_s) -->
       </div>
       <div class="slide">             <!-- Repeat group item (slides_o[1]) -->
         <img src="slide2.png" alt=""> <!-- Repeat group item field (slides_o[1].images_s) -->
         <h2>Slide Two</h2>            <!-- Repeat group item field (slides_o[1].caption_s) -->
       </div>
     </div>
   </div>

Assume you're using a particular *CarouselJS* library that requires the ``div.carousel`` element to be
the direct parent of the ``div.slide`` elements. As mentioned earlier, we can flip around the elements
for the component and the repeat group.

.. code-block:: html
   :linenos:

   <div>                    <!-- Component (Carousel) -->
     <div class="carousel"> <!-- Repeating group (slides_o) -->
       ...
     </div>
   </div>

Now, to start converting the elements to be editable, replace each tag, with the appropriate CrafterCMS macro.
Prepend ``@crafter.`` to every tag so that ``<div>…</div>`` becomes ``<@crafter.div>...</@crafter.div>``,
``<h1>`` becomes ``<@crafter.h1>``, ``<img>`` becomes ``<@crafter.img>``, ``span`` becomes ``<@crafter.span>``
and so on.

Exceptions to this are the following:

* For repeat group field elements and their children, use ``@crafter.renderRepeatGroup``.
* For item selector controls that hold components to be rendered, use ``@crafter.renderComponentCollection``.

To convert the carousel example, first, mark the component root by using ``@crafter.div``.
See :ref:`newIa-htmlElementTagMacros` for all the available customizations and configuration.

.. code-block:: text

   <#import "/templates/system/common/crafter.ftl" as crafter />
   <@crafter.div>
     ...
   </@crafter.div>

Next, let's do the repeat group and its items. We use ``@crafter.renderRepeatGroup`` to render repeat
groups. :ref:`newIa-renderRepeatGroup` for all the available customizations and configuration.

.. code-block:: text
   :linenos:

   <@crafter.renderRepeatGroup
     $field="slides_o"
     $containerAttributes={ "class": "carousel" }
     $itemAttributes={ "class": "slide" };
     item, index
   >
     <@crafter.img
       $field="slides_o.image_s"
       $index="${index}"
       src="${item.image_s}"
       alt=""
     />
     <@crafter.h2 $field="slides_o.caption_s" $index="${index}">
       ${item.caption_html!''}
     </@crafter.h2>
   </@crafter.renderRepeatGroup>

The ``renderRepeatGroup`` macro does several things for us:


* Prints the repeat group *container element*
* Prints the repeat group *item elements*
* Per-item, prints out what you pass down as the body (i.e. ``<#nested />``) to the macro

  * It provides you with the ``item`` and ``index`` for each item, so you can use them appropriately as if you were
    iterating manually.

The complete FreeMarker template for the carousel component becomes:

.. code-block:: text
   :linenos:

   <#import "/templates/system/common/crafter.ftl" as crafter />
   <@crafter.componentRootTag>
     <@crafter.renderRepeatGroup
       $field="slides_o"
       $containerAttributes={ "class": "carousel" }
       $itemAttributes={ "class": "slide" };
       item, index
     >
       <@crafter.img
         $field="slides_o.image_s"
         $index="${index}"
         src="${item.image_s!''}"
         alt=""
       />
       <@crafter.h2 $field="slides_o.caption_s" $index="${index}">
         ${item.caption_html!''}
       </@crafter.h2>
     </@crafter.renderRepeatGroup>
   </@crafter.componentRootTag>

.. TODO Speak about the ice support classes, event capture overlay and special treatment for empty zones

.. _newIa-xbMacros:

FreeMarker Macros & Utilities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are three macros in ``crafter.ftl``:

- ``head``: used to inject templates from plugins
- ``body_top``: used to inject templates from plugins
- ``body_bottom``: used to inject templates from plugins and is also used by ICE as detailed below

The ``head``, ``body_bottom`` and ``body_top`` are macros that should be positioned in those positions that the
name suggests.  Their purpose is to print strategic scripts, stylesheets or otherwise executions that should
take place in those moments of the page rendering or be printed in that position.
Plugins use these “hooks” to inject themselves on the right location so it’s important for ftl templates to
position them in accordance to their name. For example, a Google Tag Manager plugin will want to get injected
early on in the ``head`` so it will print it’s script in the ``<@head />`` hook.


See :ref:`here <newIa-plugins-using-freemarker-templates>` for more information on injecting templates from plugins.

|

After importing ``crafter.ftl``, you'll have all the available XB macros described below.

.. code-block:: text

   <#import "/templates/system/common/crafter.ftl" as crafter />


.. TODO eventCaptureOverlay $onlyInPreview=false $tag="div" $attributes={} attrs
   const editModeClass = 'craftercms-ice-on';
   const zKeyClass = 'craftercms-ice-bypass';

.. _newIa-freemarker-initExperienceBuilder:

initExperienceBuilder
"""""""""""""""""""""

Initializes the ICE engine and the communication between the page/app and studio. Call is required to
enable Studio to control the page and for XB to enable ICE.

The ``initExperienceBuilder`` macro is automatically invoked by the ``<@crafter.body_bottom />`` but you can opt out
of it by invoking body_bottom with ``initializeInContextEditing=false``.

.. code-block:: text

   <@crafter.body_bottom initializeInContextEditing=false />

In that case, you'll need to invoke ``initExperienceBuilder`` manually.

.. list-table::
   :widths: 10 10 80
   :header-rows: 1

   * - Parameter
     - Type
     - Description
   * - isAuthoring
     - boolean
     - Optional as it defaults to :ref:`modePreview <newIa-printIfPreview>` FreeMarker context variable. When isAuthoring=false, in context editing is skipped all together. Meant for running in production.
   * - props
     - JS object string
     - This is passed directly to the JavaScript runtime. Though it should be passed to the macro as a string, the contents of the string should be a valid JavaScript object. Use it to configure/customize Crafter's JavaScript libraries initialization.


Examples
""""""""

.. TODO Add context to the examples below or find a way to make these look better/more meaningful when rendered

.. code-block:: text

   <@initExperienceBuilder />


.. code-block:: text

   <@initExperienceBuilder props="{ themeOptions: { ... } }" />


.. code-block:: text

   <@crafter.body_bottom xbProps="{ scrollElement: '#mainWrapper' }" />
   <#-- `body_bottom` internally invokes `initExperienceBuilder` -->

.. _newIa-htmlElementTagMacros:

Html elements tag macros
""""""""""""""""""""""""

CrafterCMS provides a comprehensive list of macros for the most common html elements that are used to
develop content-managed websites/webapps. All these tags provided are essentially an alias to the
underlying ``@crafter.tag`` macro, which you can use when you wish to use an element that isn't provided
in the out-of-the-box macros (e.g. if you're using custom html elements), or if you need to set which
tag to use dynamically (see examples below).

The following tags are available:

``article``, ``a``, ``img``, ``header``, ``footer``, ``div``, ``section``, ``span``, ``h1``, ``h2``, ``h3``, ``h4``, ``h5``,
``h6``, ``ul``, ``p``, ``ul``, ``li``, ``ol``, ``iframe``, ``em``, ``strong``, ``b``, ``i``, ``small``, ``th``, ``caption``, ``tr``,
``td``, ``table``, ``abbr``, ``address``, ``aside``, ``audio``, ``video``, ``blockquote``, ``cite``, ``em``, ``code``, ``nav``,
``figure``, ``figcaption``, ``pre``, ``time``, ``map``, ``picture``, ``source``, ``meta``, ``title``

.. TODO review the description

.. list-table::
   :widths: 10 90
   :header-rows: 1

   * - Parameter
     - Description
   * - ``$model``
     - The content model for which this element belongs to. ``$model`` is defaulted to the ``contentModel`` FreeMarker
       template context variable denoting the current page or component, so in most cases it is not necessary
       to specify it. This is only required to be specified if you're trying to use a different model than the default
   * - ``$field``
     - The field ID on the content type definition of the current model. When inside repeat groups,
       a dot-separated-string of the full field *path* to the present field (e.g. ``slides_o.image_s``)
   * - ``$index``
     - When inside a collection (i.e. repeat group or component collection), the index of the present item. When nested
       inside repeat groups, the full index *path* to this item (e.g. ``0.1``)
   * - Html attributes
     - For convenience, macro tags will print out to the HTML all the attributes you pass to them that aren't one of
       the Crafter custom arguments (i.e. ``$model``, ``$field``, etc). For example, if you have
       ``<div class="carousel">``, you can convert to a Crafter tag like ``<@crafter.div class="carousel" ...>``.
       If you use attributes that go against FreeMarker syntax (e.g. ``data-my-attribute="foo"``), use the
       ``$attributes`` argument of the macros instead
   * - ``$attributes``
     - Html attributes to print on to the element. Particularly useful for attributes that you can't supply to
       the macro as a direct argument due to FreeMarker syntax restrictions. For example, ``<div data-foo="bar">``,
       transforming it as ``<@crafter.div data-foo="bar" ...>`` would produce a FreeMarker exception;
       use ``<@crafter.div $attrs={ "data-foo": "bar" } ...>`` instead
   * - ``$tag``
     - Specify which tag to use. For example ``<@crafter.tag $tag="article"... />`` will print out an
       ``<article>`` tag. Use only if you're using ``@crafter.tag``, which in most cases you don't need to as you
       can use the tag alias (e.g. ``<@crafter.article ... />``)


Examples
########

In a component template no ``$field`` is necessary for the component root tag as it is not a field; it's
a model. Also, no ``$model`` since by default it already uses ``contentModel``; and, no ``$index`` since it's not
an item of a collection.

.. code-block:: text
   :emphasize-lines: 1

   <@crafter.section>
     <@crafter.h1 $field="heading_t">${contentModel.heading_t}</@crafter.h1>
   </@crafter.section>

In this example, a dynamic tag is used to print the tag from the actual content model.

.. code-block:: text
   :emphasize-lines: 1

   <@crafter.tag $tag=(contentModel.headingLevel_s!'h2')>
     <@crafter.span $field"text_s">${contentModel.text_s}</@crafter.span>
   </@crafter.tag>

.. _newIa-renderComponentCollection:

renderComponentCollection
"""""""""""""""""""""""""

Used to render *Item Selector* controls, which hold components. Internally, it prints out the
tag for the field (item selector) and the tags for each of the component container items.

The way component collections are modelled on the ICE engine are in the following hierarchy:

.. code-block:: text

   <FieldTag>
     <Item0>
       <ComponentTag>
         ...
     <Item1>
       <ComponentTag>
         ...
     <Item2>
       <ComponentTag>
         ...

Note that the item tag is not the component tag itself, instead, the component is contained by the item and it's
not the item.

.. list-table::
   :widths: 10 90
   :header-rows: 1

   * - Parameters
     - Description
   * - ``$model``
     - The content model for which this element belongs to. ``$model`` is defaulted to the ``contentModel`` FreeMarker
       template context variable denoting the current page or component, so in most cases it is not necessary
       to specify it. This is only required to be specified if you're trying to use a different model than the default
   * - ``$field``
     - The field ID on the content type definition of the current model. When inside repeat groups,
       a dot-separated-string of the full field *path* to the present field (e.g. ``slides_o.image_s``)
   * - ``$index``
     - When inside a collection (i.e. repeat group or component collection), the index of the present item. When nested
       inside repeat groups, the full index *path* to this item (e.g. ``0.1``)
   * - ``$fieldCarryover``
     - When nested inside repeat groups, a dot-separated-string of the full field *path* to the present field
       (e.g. ``repeatOne_o.repeatTwo_s``) **without the current field itself**, as the macro puts them together.
   * - ``$indexCarryover``
     - When nested inside repeat groups, the full index *path* to this control (e.g. ``0.1``).
   * - ``$collection``
     - Contains the collection that the macro iterates through internally. By default, it is set to ``$model[$field]``,
       so not required to specify in most cases; however, you can manually specify the collection that will be looped
       when invoking the macro if you need to.
   * - ``$containerAttributes``
     - Html attributes to print on to the **field** element.
   * - ``$containerTag``
     - The tag to use for the **field** element.
   * - ``$itemTag``
     - The tag to use for the **item**  tags.
   * - ``$itemAttributes``
     - Html attributes to print on to the **item** elements.
   * - ``$nthItemAttributes``
     - Html attributes to print by item index. For example, ``$nthItemAttributes={ 0: { "class": "active" } }`` will
       apply the class named active only to the first item in the collection.
   * - ``renderComponentArguments``
     - CrafterCMS' :ref:`renderComponent <newIa-renderComponent>` macro supports supplying additional arguments
       (``additionalModel`` argument when used directly) to the component template context. You can send these via
       this parameter. The ``renderComponentArguments`` will be sent to all items.

Example
#######

.. code-block:: text

   <@crafter.renderComponentCollection $field="mainContent_o" />

The sample above would print out the following html:

.. code-block:: html

   <!-- Field element -->
   <section
     data-craftercms-model-path="/site/website/index.xml"
     data-craftercms-model-id="8d7f21fa-5e09-00aa-8340-853b7db302da"
     data-craftercms-field-id="mainContent_o"
   >
     <!-- Item 0 element -->
     <div
       data-craftercms-model-path="/site/website/index.xml"
       data-craftercms-model-id="8d7f21fa-5e09-00aa-8340-853b7db302da"
       data-craftercms-field-id="mainContent_o"
       data-craftercms-index="0"
     >
       <!-- Component @ Item 0 -->
       <div
         data-craftercms-model-path="/site/components/component_hero/bd283e3b-3484-6b9e-b2d5-2a9e87128b69.xml"
         data-craftercms-model-id="bd283e3b-3484-6b9e-b2d5-2a9e87128b69"
       >
         ...
       </div>
     </div>
     <!-- Item 1 element -->
     <div
       data-craftercms-model-path="/site/website/index.xml"
       data-craftercms-model-id="8d7f21fa-5e09-00aa-8340-853b7db302da"
       data-craftercms-field-id="mainContent_o"
       data-craftercms-index="1"
     >
       <!-- Component @ Item 1 -->
       <div
         data-craftercms-model-path="/site/website/index.xml"
         data-craftercms-model-id="2e8761a9-1268-581b-f8d0-52cad6a73e0a"
       >
         ...
       </div>
     </div>
   </section>

.. _newIa-renderRepeatGroup:

renderRepeatGroup
"""""""""""""""""

Used to render *Repeat Group* controls. Internally, it prints out the
tag for the field (repeat group) and the tags for each of the items.

The way repeat group collections are modelled on the ICE engine are in the following hierarchy:

.. code-block:: text

   <FieldTag>
     <Item0>
         ...
     <Item1>
         ...
     <Item2>
       <ComponentTag>
         ...
     ...

Repeat groups introduce the possibility of having complex/compound ``$field`` and ``$index`` arguments when they
contain nested repeat groups or component collections.

.. list-table::
   :widths: 10 90
   :header-rows: 1

   * - Parameters
     - Description
   * - ``$model``
     - The content model for which this element belongs to. ``$model`` is defaulted to the ``contentModel`` FreeMarker
       template context variable denoting the current page or component, so in most cases it is not necessary
       to specify it. This is only required to be specified if you're trying to use a different model than the default
   * - ``$field``
     - The field ID on the content type definition of the current model. When inside repeat groups,
       a dot-separated-string of the full field *path* to the present field (e.g. ``slides_o.image_s``)
   * - ``$index``
     - When inside a collection (i.e. repeat group or component collection), the index of the present item. When nested
       inside repeat groups, the full index *path* to this item (e.g. ``0.1``)
   * - ``$fieldCarryover``
     - When nested inside repeat groups, a dot-separated-string of the full field *path* to the present field
       (e.g. ``repeatOne_o.repeatTwo_s``) **without the current field itself**, as the macro puts them together.
   * - ``$indexCarryover``
     - When nested inside repeat groups, the full index *path* to this control (e.g. ``0.1``).
   * - ``$collection``
     - Contains the collection that the macro iterates through internally. By default, it is set to ``$model[$field]``,
       so not required to specify in most cases; however, you can manually specify the collection that will be looped
       when invoking the macro if you need to.
   * - ``$containerAttributes``
     - Html attributes to print on to the **field** element.
   * - ``$containerTag``
     - The tag to use for the **field** element.
   * - ``$itemTag``
     - The tag to use for the **item**  tags.
   * - ``$itemAttributes``
     - Html attributes to print on to the **item** elements.
   * - ``$nthItemAttributes``
     - Html attributes to print by item index. For example, ``$nthItemAttributes={ 0: { "class": "active" } }`` will
       apply the class named active only to the first item in the collection.

Examples
########

.. code-block:: text

   <@crafter.renderRepeatCollection
     $containerTag="section"
     $containerAttributes={ "class": "row" }
     $itemTag="div"
     $itemAttributes={ "class": "col" }
     $field="columns_o";
     <#-- Nested content values passed down by the macro: -->
     item, index
   >
     <@crafter.renderComponentCollection
       $field="items_o"
       $fieldCarryover="columns_o"
       $indexCarryover="${index}"
       $model=(contentModel + { "items_o": item.items_o })
     />
   </@crafter.renderRepeatCollection>

The sample above would print out the following html:

.. code-block:: html

   <!-- The repeat group field element (columns_o) -->
   <section
     class="row"
     data-craftercms-model-path="/site/website/index.xml"
     data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
     data-craftercms-field-id="columns_o"
   >
     <!-- Repeat group item 0 element (i.e. columns_o[0]) -->
     <div
       class="col"
       data-craftercms-model-path="/site/website/index.xml"
       data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
       data-craftercms-field-id="columns_o"
       data-craftercms-index="0"
     >
       <!-- An item selector field named `items_o` that's inside the repeat group (i.e. columns_o[0].items_o) -->
       <div
         data-craftercms-model-path="/site/website/index.xml"
         data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
         data-craftercms-field-id="columns_o.items_o"
         data-craftercms-index="0"
       >
         <!-- columns_o[0].items_o[0] -->
         <div
           data-craftercms-model-path="/site/website/index.xml"
           data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
           data-craftercms-field-id="columns_o.items_o"
           data-craftercms-index="0.0"
         >
           <!-- Embedded component hosted @ columns_o[0].items_o[0] -->
           <h2
             class="heading-component-root"
             data-craftercms-model-path="/site/website/index.xml"
             data-craftercms-model-id="57a30ade-f167-5a8b-efbe-30ceb0771667"
           >
             <span
               data-craftercms-model-path="/site/website/index.xml"
               data-craftercms-model-id="57a30ade-f167-5a8b-efbe-30ceb0771667"
               data-craftercms-field-id="text_s"
             >
               This is a heading
             </span>
           </h2>
         </div>
         <!-- columns_o[0].items_o[1] -->
         <div
           data-craftercms-model-path="/site/website/index.xml"
           data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
           data-craftercms-field-id="columns_o.items_o"
           data-craftercms-index="0.1"
         >
           <!-- Embedded component hosted @ columns_o[0].items_o[1] -->
           <div
             class="paragraph-component-root"
             data-craftercms-model-path="/site/website/index.xml"
             data-craftercms-model-id="fff36233-34d9-f476-0a35-00b507b9420b"
           >
             <p
               data-craftercms-model-path="/site/website/index.xml"
               data-craftercms-model-id="fff36233-34d9-f476-0a35-00b507b9420b"
               data-craftercms-field-id="copy_t"
             >
               Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
               eiusmod tempor incididunt ut labore et dolore magna aliqua.
             </p>
           </div>
         </div>
       </div>
     </div>
     <!-- Repeat group item 1 element (i.e. columns_o[1]) -->
     <div
       class="col"
       data-craftercms-model-path="/site/website/index.xml"
       data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
       data-craftercms-field-id="columns_o"
       data-craftercms-index="1"
     >
       <!-- An item selector field named `items_o` that's inside the repeat group (i.e. columns_o[1].items_o) -->
       <div
         data-craftercms-model-path="/site/website/index.xml"
         data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
         data-craftercms-field-id="columns_o.items_o"
         data-craftercms-index="1"
       >
         <!-- columns_o[1].items_o[0] -->
         <div
           data-craftercms-model-path="/site/website/index.xml"
           data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
           data-craftercms-field-id="columns_o.items_o"
           data-craftercms-index="1.0"
         >
           <!-- Embedded component hosted @ columns_o[1].items_o[0] -->
           <span
             data-craftercms-model-path="/site/website/index.xml"
             data-craftercms-model-id="eb50be40-5755-5dfa-0ad0-15367b5cc685"
           >
             <img
               src="https://place-hold.it/300"
               alt=""
               class=""
               data-craftercms-model-path="/site/website/index.xml"
               data-craftercms-model-id="eb50be40-5755-5dfa-0ad0-15367b5cc685"
               data-craftercms-field-id="image_s"
             >
           </span>
         </div>
         <!-- columns_o[1].items_o[0] -->
         <div
           data-craftercms-model-path="/site/website/index.xml"
           data-craftercms-model-id="f830b94f-a6e9-09eb-9978-daafbfdf63ef"
           data-craftercms-field-id="columns_o.items_o"
           data-craftercms-index="1.1"
         >
           <!-- Embedded component hosted @ columns_o[1].items_o[1] -->
           <div
             class="paragraph-component-root"
             data-craftercms-model-path="/site/website/index.xml"
             data-craftercms-model-id="4b68e47a-07a3-134f-a540-1b7907080cb0"
           >
             <p
               data-craftercms-model-path="/site/website/index.xml"
               data-craftercms-model-id="4b68e47a-07a3-134f-a540-1b7907080cb0"
               data-craftercms-field-id="copy_t"
             >
               Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do
               eiusmod tempor incididunt ut labore et dolore magna aliqua.
             </p>
           </div>
         </div>
       </div>
     </div>
   </section>

.. _newIa-forEach:

forEach
"""""""

Useful for iterating through crafter collections.

Examples
########

.. code-block:: text

   <@crafter.forEach contentModel.slides_o; slide, index>
     <#assign
       attributesByIndex = attributesByIndex + { index: { "data-bs-interval": "${slide.delayInterval_i?c}" } }
     />
   </@crafter.forEach>

.. code-block:: text

   <@crafter.forEach contentModel.slides_o; slide, index>
     <button
       type="button"
       data-bs-target="#${rootElementId}"
       data-bs-slide-to="${index}"
       aria-label="Slide ${index}"
       ${(initialActiveSlideIndex == index)?then('class="active" aria-current="true"', '')}
     ></button>
   </@crafter.forEach>

.. _newIa-cleanDotNotationString:

cleanDotNotationString
""""""""""""""""""""""

Takes a dot-separated-string and returns a string that doesn't have any dots at the beginning or
end of the string and that there aren't any consecutive dots.

Useful when working with repeat groups in Crafter as these introduce the possibility of field/index
carryovers and complex/compound fields (e.g. ``field1.field2``) and indexes (e.g. ``0.1``).

.. code-block:: text

   <#assign str1 = ".hello." />
   <#assign str2 = ".world." />
   ${crafter.cleanDotNotationString("${str1}.${str2}")}
   <#-- Output is hello.world -->

.. code-block:: text

   ${crafter.cleanDotNotationString("...foo...bar..")}
   <#-- Output is foo.bar -->

.. code-block:: text

   ${crafter.cleanDotNotationString("..")}
   <#-- Output is an empty string -->

.. _newIa-isEmptyCollection:

isEmptyCollection
"""""""""""""""""

Receives a Crafter collection and returns true if it's empty or false otherwise.

.. _newIa-emptyCollectionClass:

emptyCollectionClass
""""""""""""""""""""

Receives a collection and, if the collection is empty it will print a *special* crafter class,
otherwise, it won't print anything. This macro only prints in Crafter Engine's *preview mode*.

The *special* class adds styles to the element so that it has a minimum height and
width so that authors can visualize the area and drag components on it despite being empty — as otherwise,
it would be invisible and virtually not editable.

One should use this macro on empty component or repeat group collections.

**Component collection**

.. code-block:: text

   <@crafter.renderComponentCollection
     $field="mainContent_o"
     $containerAttributes={ "class": crafter.emptyCollectionClass(contentModel.mainContent_o) }
   />

**Repeat group**

.. code-block:: text

   <@crafter.renderRepeatGroup
     $field="slides_o"
     $containerAttributes={ "class": crafter.emptyCollectionClass(contentModel.slides_o) }
   />

.. _newIa-emptyFieldClass:

emptyFieldClass
"""""""""""""""

Receives a field value and, if the field has no content it will print a *special* crafter class,
otherwise, it won't print anything. This macro only prints in Crafter Engine's *preview mode*.

The *special* class adds styles to the element so that it has a minimum height and
width so that authors can visualize the area and add content to this field — as otherwise,
it would be invisible and virtually not editable.

One should use this macro on empty fields.

**Example**

.. code-block:: text

   <@crafter.h1
      class="display-5 fw-bold ${crafter.emptyFieldClass(contentModel.title_s)}"
      $field="title_s"
   >
      ${contentModel.title_s!''}
   </@crafter.h1>

.. _newIa-printIfPreview:

printIfPreview
""""""""""""""

Receives a string which it will print if Crafter Engine is running in preview mode. Doesn't print
anything if Engine is running the published site.

.. code-block:: text

   <#-- Import the "debug" version of the script in preview. -->
   <script src="/static-assets/js/bootstrap.bundle${crafter.printIfPreview('.debug')}.js"></script>

You can also use the FreeMarker context variable ``modePreview`` to do similar things; in fact,
``printIfPreview`` uses it internally.

.. code-block:: text

   <#-- Import a in-context editing stylesheet only in preview. -->
   <#if modePreview><link href="/static-assets/css/ice.css" rel="stylesheet"></#if>

.. _newIa-printIfNotPreview:

printIfNotPreview
"""""""""""""""""

Receives a string which it will print if Crafter Engine is not running in preview mode. Doesn't print
anything if Engine is running the published site.

.. code-block:: text

   <#-- Import the "minified" version of the script in delivery. -->
   <script src="/static-assets/js/bootstrap.bundle${crafter.printIfNotPreview('.min')}.js"></script>

.. _newIa-navigation:

navigation
""""""""""

Prints out the navigation structure of a site in a customizable markup structure.

.. list-table::
   :widths: 10 10 10 70
   :header-rows: 1

   * - Parameter
     - Type
     - Default
     - Description
   * - ``url``
     - string
     - "/site/website"
     - The URL path to start printing breadcrumbs from
   * - ``showNavElement``
     - boolean
     - true
     - Whether to print a ``nav`` element wrapping the whole nav structure
   * - ``navElementClass``
     - string
     - ""
     - Class(es) to apply to the ``nav`` element
   * - ``containerElement``
     - string
     - "ul"
     - Parent tag for the nav items and nav item wrappers. Will be skipped if set to an empty string (i.e. ``""``)
   * - ``containerElementClass``
     - string
     - ""
     - Class(es) applied to the container element.
   * - ``itemWrapperElement``
     - string
     - "li"
     - Element used to wrap links (e.g. in ``<li><a /></li>`` the ``li`` wraps the ``a``). Will be skipped if set
       to an empty string (i.e. ``""``).
   * - ``itemWrapperClass``
     - string
     - ""
     - Attributes added to the nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemWrapperActiveClass``
     - string
     - "active"
     - Class(es) added to the active nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemWrapperAttributes``
     - hash
     - {}
     - Attributes added to all nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemClass``
     - string
     - ""
     - Class(es) added to all nav item elements.
   * - ``itemActiveClass``
     - string
     - "active"
     - Class(es) added to the active page (i.e. the page the user is on).
   * - ``itemAttributes``
     - hash
     - {}
     - Attributes applied to the nav items.
   * - ``hasSubItemItemClass``
     - string
     - ""
     - Class(es) applied to those items that have children. Applied to the nav item, not it's wrapper.
   * - ``hasSubItemWrapperClass``
     - string
     - ""
     - Class(es) applied to the wrapper of those items that have children.
   * - ``hasSubItemItemAttributes``
     - hash
     - {}
     - Attributes applied to items that have children.
   * - ``subItemClass``
     - string
     - ""
     - Class(es) applied to items that are at least one level "down".
   * - ``subItemClassPrefix``
     - string
     - "nav-level"
     - A class is created dynamically in the form of ``${subItemClassPrefix}-${currentDepth}``. You may customize
       the subItemClassPrefix to change the default from ``nav-level-${depth}`` to ``${whatEverYouPlease}-${depth}``.
   * - ``subItemAttributes``
     - hash
     - {}
     - Attributes applied to the items that are at least one level "down".
   * - ``subItemWrapperClass``
     - string
     - ""
     - Class(es) applied to the wrapper of those items that are at least one level "down".
   * - ``subItemWrapperClassPrefix``
     - string
     - ""
     - **If specified**, a class is created dynamically in the form of ``${subItemWrapperClassPrefix}-${currentDepth}``.
   * - ``subItemContainerClass``
     - string
     - ""
     - Class(es) applied to the container at each depth level.
   * - ``depth``
     - number
     - 1
     - How many depth levels to print.
   * - ``includeRoot``
     - boolean
     - true
     - Whether to print the root of the nav. For example, you may want to print the children of "Home" without Home
       itself, in which case you'd set to false.
   * - ``inlineRootWithImmediateChildren``
     - boolean
     - true
     - Whether to print the root item on the same level as it's immediate children. For example you may want to
       print ``Home`` at the same level as its children to get something like ``Home • Products • About • Contact``
       instead of having products, about and contact as a dropdown or indented within home in your UI.

.. _newIa-navigationItem:

navigationItem
""""""""""""""

Used internally by `the navigation macro <#navigation>`_ to print each item.

See the navigation macro

.. list-table::
   :widths: 10 10 10 70
   :header-rows: 1

   * - Parameter
     - Type
     - Default
     - Description
   * - *
     -
     -
     - See parameters for `the navigation macro <#navigation>`_ as they are the same.
   * - ``currentDepth``
     - number
     - 0
     - The current level of depth that will get printed by this macro.
   * - ``navItem``
     - object
     - {}
     - The navItem object that will be used to print.

.. _newIa-breadcrumb:

breadcrumb
""""""""""

.. list-table::
   :widths: 10 10 10 70
   :header-rows: 1

   * - Parameter
     - Type
     - Default
     - Description
   * - ``url``
     - string
     - "/site/website"
     - The current URL used to build the breadcrumb.
   * - ``root``
     - string
     - "/site/website"
     - The starting point (root) of the breadcrumb
   * - ``showNavElement``
     - boolean
     - true
     - Whether to wrap the whole navigation structure on a ``<nav />`` element.
   * - ``navElementClass``
     - string
     - ""
     - Class(es) added to the ``nav`` element.
   * - ``navElementAttributes``
     - hash
     - {}
     - Attributes added to the ``nav`` element.
   * - ``containerElement``
     - string
     - "ul"
     - Parent tag for the nav items and nav item wrappers. Will be skipped if set to an empty string (i.e. ``""``).
   * - ``containerElementClass``
     - string
     - ""
     - Class(es) applied to the container element.
   * - ``itemWrapperElement``
     - string
     - "li"
     - Element used to wrap links (e.g. in ``<li><a /></li>`` the ``li`` wraps the ``a``). Will be skipped if set
       to an empty string (i.e. ``""``).
   * - ``itemWrapperClass``
     - string
     - ""
     - Attributes added to the nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemWrapperActiveClass``
     - string
     - "active"
     - Class added to the active nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemWrapperAttributes``
     - hash
     - {}
     - Attributes added to all nav item link wrapper (e.g. the ``li`` that wraps the ``a``).
   * - ``itemClass``
     - string
     - ""
     - Class(es) added to all nav item elements.
   * - ``itemAttributes``
     - hash
     - {}
     - Attributes added to all nav item elements.
   * - ``includeLinkInActiveItem``
     - boolean
     - false
     - Whether to render the active element as a link (i.e. ``a``); otherwise rendered as a ``span``.

