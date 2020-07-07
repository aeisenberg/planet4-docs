---
description: Development documentation on P4 WYSIWYG Gutenberg blocks
---

# Blocks

## Rendering Blocks in the Frontend

We decided to include **React** in the **Frontend** in order to render the blocks. This avoids having duplicate template logic, otherwise we would have a JSX version of the block for the editor, and a Twig template for the frontend.

The **`save()`** method is a client-side method expected to output static content, which is persisted as the block's content in the database. The usual way of rendering a dynamic block in Gutenberg is returning null in the block's [save\(\)](https://developer.wordpress.org/block-editor/developers/block-api/block-edit-save/#save) function, then using the [ServerSideRender](https://developer.wordpress.org/block-editor/packages/packages-server-side-render/) component and **`render_callback()`**when [registering the block](https://developer.wordpress.org/reference/functions/register_block_type/) in the PHP side. This is the way our blocks are currently rendered, thus it's the method we are deprecating in favor of the React approach.

As any changes in the block's `save()` method would trigger a [validation error](https://developer.wordpress.org/block-editor/developers/block-api/block-edit-save/#validation) in the block editor, we first needed to add a deprecated property to the block, to let WP know that we are OK with our block changing from null to something else.

This is just like telling Wordpress: "Hey, this used to return **null**, now we are going to return something else but don't worry, it's intentional".

To accomplish this, we created two components: **frontendRendered** and **FrontendBlockNode**.

[frontendRendered](https://github.com/greenpeace/planet4-plugin-gutenberg-blocks/blob/master/assets/src/blocks/Spreadsheet/SpreadsheetBlock.js#L39) is meant to replace the `save()` function in a block definition.

It returns a function with the same parameters as the `save()` function, so it takes two arguments: **attributes** and **className**.

So the function returns a [FrontendBlockNode](https://github.com/greenpeace/planet4-plugin-gutenberg-blocks/blob/master/assets/src/components/FrontendBlockNode/FrontendBlockNode.js#L10) which renders an **empty div** with some **data attributes**, which will be the placeholder for the block in the frontend site. This content is static and can be persisted safely to the database.

So Wordpress will only render that **div** with its attributes in the frontend site, then it's our job to make React render something inside of it.

This is done in the [frontendIndex](https://github.com/greenpeace/planet4-plugin-gutenberg-blocks/blob/master/assets/src/frontendIndex.js#L12) file, which has a new entry point in our Webpack config, and iterates through these placeholder divs to render the blocks inside using React.

{% hint style="info" %}
🔧 This page is still in progress.
{% endhint %}

