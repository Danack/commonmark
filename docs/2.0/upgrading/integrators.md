---
layout: default
title: Upgrading from 1.5 to 2.0 (for integrators)
description: Integrator guide to upgrading to newer versions of this library
---

## Minimum PHP Version

The minimum supported PHP version was increased from 7.1 to 7.2.

## `CommonMarkConverter` and `GithubFlavoredMarkdownConverter` constructors

The constructor methods for both `CommonMarkConverter` and `GithubFlavoredMarkdownConverter` no longer accept passing in a customized `Environment`.  If you want to customize the extensions used in your converter you should switch to using `MarkdownConverter`. See the [Basic Usage](/2.0/basic-usage/) documentation for an example.

## `CommonMarkConverter` Return Type

In 1.x, calling `convertToHtml()` would return a `string`. In 2.x this changed to return a `RenderedContentInterface`.  To get the resulting HTML, either cast the return value to a `string` or call `->getContent()`.  (This new interface extends from `Stringable` so you can type hint against that instead, if needed.)

```diff
 use League\CommonMark\CommonMarkConverter;

 $converter = new CommonMarkConverter();
-echo $converter->convertToHtml('# Hello World!');
+echo $converter->convertToHtml('# Hello World!')->getContent();
+// or
+echo (string) $converter->convertToHtml('# Hello World!');
```

## HTML Changes

Table of Contents items are no longer wrapped with `<p>` tags:

```diff
 <ul class="table-of-contents">
     <li>
-        <p><a href="#level-2-heading">Level 2 Heading</a></p>
+        <a href="#level-2-heading">Level 2 Heading</a>
     </li>
     <li>
-        <p><a href="#level-4-heading">Level 4 Heading</a></p>
+        <a href="#level-4-heading">Level 4 Heading</a>
     </li>
     <li>
-        <p><a href="#level-3-heading">Level 3 Heading</a></p>
+        <a href="#level-3-heading">Level 3 Heading</a>
     </li>
 </ul>
```

See [#613](https://github.com/thephpleague/commonmark/issues/613) for details.

## Configuration Option Changes

Several configuration options now have new names:

| Old Key/Path                       | New Key/Path                        | Notes                                   |
| ---------------------------------- | ----------------------------------- | --------------------------------------- |
| `enable_em`                        | `commonmark/enable_em`              |                                         |
| `enable_strong`                    | `commonmark/enable_strong`          |                                         |
| `use_asterisk`                     | `commonmark/use_asterisk`           |                                         |
| `use_underscore`                   | `commonmark/use_underscore`         |                                         |
| `unordered_list_markers`           | `commonmark/unordered_list_markers` | Empty arrays no longer allowed          |
| `heading_permalink/inner_contents` | `heading_permalink/symbol`          |                                         |
| `max_nesting_level`                | (unchanged)                         | Only integer values are supported       |
| `mentions/*/symbol`                | `mentions/*/prefix`                 |                                         |
| `mentions/*/regex`                 | `mentions/*/pattern`                | Cannot contain start/end `/` delimiters |

## Method Return Types

Return types have been added to virtually all class and interface methods.  If you implement or extend anything from this library, ensure you also have the proper return types added.

## Classes/Namespaces Renamed

Many classes now live in different namespaces, and some have also been renamed.  Here's a quick guide showing their new locations:

_(Note that the base namespace of `League\CommonMark` has been omitted from this table for brevity.)_

| Old Class Namespace/Name (1.x)                                                        | New Class Namespace/Name (2.0)                                                           |
| ------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `Util\ConfigurationAwareInterface`                                                    | `Configuration\ConfigurationAwareInterface`                                              |
| `Util\ConfigurationInterface`                                                         | `Configuration\ConfigurationInterface`                                                   |
| `Util\Configuration`                                                                  | `Configuration\Configuration`                                                            |
| `ConfigurableEnvironmentInterface`                                                    | `Environment\ConfigurableEnvironmentInterface`                                           |
| `EnvironmentAwareInterface`                                                           | `Environment\EnvironmentAwareInterface`                                                  |
| `Environment`                                                                         | `Environment\Environment`                                                                |
| `EnvironmentInterface`                                                                | `Environment\EnvironmentInterface`                                                       |
| `Extension\CommonMarkCoreExtension`                                                   | `Extension\CommonMark\CommonMarkCoreExtension`                                           |
| `Delimiter\Processor\EmphasisDelimiterProcessor`                                      | `Extension\CommonMark\Delimiter\Processor\EmphasisDelimiterProcessor`                    |
| `Block\Element\BlockQuote`                                                            | `Extension\CommonMark\Node\Block\BlockQuote`                                             |
| `Block\Element\FencedCode`                                                            | `Extension\CommonMark\Node\Block\FencedCode`                                             |
| `Block\Element\Heading`                                                               | `Extension\CommonMark\Node\Block\Heading`                                                |
| `Block\Element\HtmlBlock`                                                             | `Extension\CommonMark\Node\Block\HtmlBlock`                                              |
| `Block\Element\IndentedCode`                                                          | `Extension\CommonMark\Node\Block\IndentedCode`                                           |
| `Block\Element\ListBlock`                                                             | `Extension\CommonMark\Node\Block\ListBlock`                                              |
| `Block\Element\ListData`                                                              | `Extension\CommonMark\Node\Block\ListData`                                               |
| `Block\Element\ListItem`                                                              | `Extension\CommonMark\Node\Block\ListItem`                                               |
| `Block\Element\ThematicBreak`                                                         | `Extension\CommonMark\Node\Block\ThematicBreak`                                          |
| `Inline\Element\AbstractWebResource`                                                  | `Extension\CommonMark\Node\Inline\AbstractWebResource`                                   |
| `Inline\Element\Code`                                                                 | `Extension\CommonMark\Node\Inline\Code`                                                  |
| `Inline\Element\Emphasis`                                                             | `Extension\CommonMark\Node\Inline\Emphasis`                                              |
| `Inline\Element\HtmlInline`                                                           | `Extension\CommonMark\Node\Inline\HtmlInline`                                            |
| `Inline\Element\Image`                                                                | `Extension\CommonMark\Node\Inline\Image`                                                 |
| `Inline\Element\Link`                                                                 | `Extension\CommonMark\Node\Inline\Link`                                                  |
| `Inline\Element\Strong`                                                               | `Extension\CommonMark\Node\Inline\Strong`                                                |
| `Block\Parser\BlockQuoteParser`                                                       | `Extension\CommonMark\Parser\Block\BlockQuoteParser`                                     |
| `Block\Parser\FencedCodeParser`                                                       | `Extension\CommonMark\Parser\Block\FencedCodeParser`                                     |
| `Block\Parser\ATXHeadingParser` and `Block\Parser\SetExtHeadingParser`                | `Extension\CommonMark\Parser\Block\HeadingParser`                                        |
| `Block\Parser\HtmlBlockParser`                                                        | `Extension\CommonMark\Parser\Block\HtmlBlockParser`                                      |
| `Block\Parser\IndentedCodeParser`                                                     | `Extension\CommonMark\Parser\Block\IndentedCodeParser`                                   |
| `Block\Parser\ListParser`                                                             | `Extension\CommonMark\Parser\Block\ListParser`                                           |
| `Block\Parser\ThematicBreakParser`                                                    | `Extension\CommonMark\Parser\Block\ThematicBreakParser`                                  |
| `Inline\Parser\AutolinkParser`                                                        | `Extension\CommonMark\Parser\Inline\AutolinkParser`                                      |
| `Inline\Parser\BacktickParser`                                                        | `Extension\CommonMark\Parser\Inline\BacktickParser`                                      |
| `Inline\Parser\BangParser`                                                            | `Extension\CommonMark\Parser\Inline\BangParser`                                          |
| `Inline\Parser\CloseBracketParser`                                                    | `Extension\CommonMark\Parser\Inline\CloseBracketParser`                                  |
| `Inline\Parser\EntityParser`                                                          | `Extension\CommonMark\Parser\Inline\EntityParser`                                        |
| `Inline\Parser\EscapableParser`                                                       | `Extension\CommonMark\Parser\Inline\EscapableParser`                                     |
| `Inline\Parser\HtmlInlineParser`                                                      | `Extension\CommonMark\Parser\Inline\HtmlInlineParser`                                    |
| `Inline\Parser\OpenBracketParser`                                                     | `Extension\CommonMark\Parser\Inline\OpenBracketParser`                                   |
| `Block\Renderer\BlockQuoteRenderer`                                                   | `Extension\CommonMark\Renderer\Block\BlockQuoteRenderer`                                 |
| `Block\Renderer\FencedCodeRenderer`                                                   | `Extension\CommonMark\Renderer\Block\FencedCodeRenderer`                                 |
| `Block\Renderer\HeadingRenderer`                                                      | `Extension\CommonMark\Renderer\Block\HeadingRenderer`                                    |
| `Block\Renderer\HtmlBlockRenderer`                                                    | `Extension\CommonMark\Renderer\Block\HtmlBlockRenderer`                                  |
| `Block\Renderer\IndentedCodeRenderer`                                                 | `Extension\CommonMark\Renderer\Block\IndentedCodeRenderer`                               |
| `Block\Renderer\ListBlockRenderer`                                                    | `Extension\CommonMark\Renderer\Block\ListBlockRenderer`                                  |
| `Block\Renderer\ListItemRenderer`                                                     | `Extension\CommonMark\Renderer\Block\ListItemRenderer`                                   |
| `Block\Renderer\ThematicBreakRenderer`                                                | `Extension\CommonMark\Renderer\Block\ThematicBreakRenderer`                              |
| `Inline\Renderer\CodeRenderer`                                                        | `Extension\CommonMark\Renderer\Inline\CodeRenderer`                                      |
| `Inline\Renderer\EmphasisRenderer`                                                    | `Extension\CommonMark\Renderer\Inline\EmphasisRenderer`                                  |
| `Inline\Renderer\HtmlInlineRenderer`                                                  | `Extension\CommonMark\Renderer\Inline\HtmlInlineRenderer`                                |
| `Inline\Renderer\ImageRenderer`                                                       | `Extension\CommonMark\Renderer\Inline\ImageRenderer`                                     |
| `Inline\Renderer\LinkRenderer`                                                        | `Extension\CommonMark\Renderer\Inline\LinkRenderer`                                      |
| `Inline\Renderer\StrongRenderer`                                                      | `Extension\CommonMark\Renderer\Inline\StrongRenderer`                                    |
| `Extension\SmartPunct\PunctuationParser`                                              | `Extension\SmartPunct\DashParser` and `Extension\SmartPunct\EllipsesParser`              |
| `Extension\TableOfContents\TableOfContents`                                           | `Extension\TableOfContents\Node\TableOfContents`                                         |
| `Block\Element\AbstractBlock`                                                         | `Node\Block\AbstractBlock`                                                               |
| `Block\Element\Document`                                                              | `Node\Block\Document`                                                                    |
| `Block\Element\InlineContainerInterface`                                              | `Node\Block\InlineContainerInterface`                                                    |
| `Block\Element\Paragraph`                                                             | `Node\Block\Paragraph`                                                                   |
| `Block\Element\StringContainerInterface`                                              | `Node\StringContainerInterface`                                                          |
| `Inline\Element\AbstractInline`                                                       | `Node\Inline\AbstractInline`                                                             |
| `Inline\Element\AbstractStringContainer`                                              | `Node\Inline\AbstractStringContainer`                                                    |
| `Inline\AdjacentTextMerger`                                                           | `Node\Inline\AdjacentTextMerger`                                                         |
| `Inline\Element\Newline`                                                              | `Node\Inline\Newline`                                                                    |
| `Inline\Element\Text`                                                                 | `Node\Inline\Text`                                                                       |
| `Block\Parser\BlockParserInterface`                                                   | `Parser\Block\BlockContinueParserInterface` and `Parser\Block\BlockStartParserInterface` |
| `Block\Parser\LazyParagraphParser`                                                    | `Parser\Block\ParagraphParser`                                                           |
| `Cursor`                                                                              | `Parser\Cursor`                                                                          |
| `DocParser`                                                                           | `Parser\MarkdownParser`                                                                  |
| `DocParserInterface`                                                                  | `Parser\MarkdownParserInterface`                                                         |
| `Inline\Parser\InlineParserInterface`                                                 | `Parser\Inline\InlineParserInterface`                                                    |
| `Inline\Parser\NewlineParser`                                                         | `Parser\Inline\NewlineParser`                                                            |
| `InlineParserContext`                                                                 | `Parser\InlineParserContext`                                                             |
| `InlineParserEngine`                                                                  | `Parser\InlineParserEngine`                                                              |
| `Block\Renderer\DocumentRenderer`                                                     | `Renderer\Block\DocumentRenderer`                                                        |
| `Block\Renderer\ParagraphRenderer`                                                    | `Renderer\Block\ParagraphRenderer`                                                       |
| `HtmlRenderer`                                                                        | `Renderer\HtmlRenderer`                                                                  |
| `ElementRendererInterface`                                                            | `Renderer\HtmlRendererInterface`                                                         |
| `Inline\Renderer\NewlineRenderer`                                                     | `Renderer\Inline\NewlineRenderer`                                                        |
| `Inline\Renderer\TextRenderer`                                                        | `Renderer\Inline\TextRenderer`                                                           |
| `Block\Renderer\BlockRendererInterface` and `Inline\Renderer\InlineRendererInterface` | `Renderer\NodeRendererInterface`                                                         |
| `HtmlElement`                                                                         | `Util\HtmlElement`                                                                       |

Most of these won't be relevant unless your integration allows people to add/remove extensions, parsers, etc. and you might reference those classes directly.  Otherwise, if you simply expose the basic classes to developers, check out the shorter, partial list in the [consumer upgrade guide](/2.0/upgrading/consumers/).

## Rendering Changes

This library no longer differentiates between block renderers and inline renderers - everything now uses "node renderers" which allow us to have a unified approach to rendering!  As a result, the following changes were made, which you may need to change in your custom extensions:

| Old Method/Interface (1.x)                              | New Method/Interface (2.0)                        |
| ------------------------------------------------------- | ------------------------------------------------- |
| `BlockRendererInterface`                                | `NodeRendererInterface`                           |
| `InlineRendererInterface`                               | `NodeRendererInterface`                           |
| `EnvironmentInterface::getBlockRenderersForClass()`     | `EnvironmentInterface::getRenderersForClass()`    |
| `EnvironmentInterface::getInlineRenderersForClass()`    | `EnvironmentInterface::getRenderersForClass()`    |
| `ConfigurableEnvironmentInterface::addBlockRenderer()`  | `ConfigurableEnvironmentInterface::addRenderer()` |
| `ConfigurableEnvironmentInterface::addInlineRenderer()` | `ConfigurableEnvironmentInterface::addRenderer()` |
| `ElementRendererInterface::renderBlock()`               | `ChildNodeRendererInterface::renderNodes()`       |
| `ElementRendererInterface::renderBlocks()`              | `ChildNodeRendererInterface::renderNodes()`       |
| `ElementRendererInterface::renderInline()`              | `ChildNodeRendererInterface::renderNodes()`       |
| `ElementRendererInterface::renderInlines()`             | `ChildNodeRendererInterface::renderNodes()`       |
| `HtmlRenderer::renderBlock($document)`                  | `HtmlRenderer::renderDocument()`                  |

Renderers now implement the unified `NodeRendererInterface` which has a similar (but slightly different) signature from
the old `BlockRendererInterface` and `InlineRendererInterface` interfaces:

```php
/**
 * @param Node                       $node
 * @param ChildNodeRendererInterface $childRenderer
 *
 * @return HtmlElement|string|null
 */
public function render(Node $node, ChildNodeRendererInterface $childRenderer);
```

The element being rendered is still passed in the first argument, and the object that helps you render children is still
passed in the second argument.  Note that blocks are no longer told whether they're being rendered in a tight list - if you
need to know about this, traverse up the `$node` AST yourself and check any `ListBlock` ancestor for tightness.

## Removed Classes

The following classes have been removed:

| Class name in 1.x              | Replacement / Notes                                                                                           |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| `AbstractStringContainerBlock` | Use `extends AbstractBlock implements StringContainerInterface` instead. Note the new method names.           |
| `Context`                      | Use `MarkdownParserState` instead (has different methods but serves a similar purpose)                        |
| `ContextInterface`             | Use `MarkdownParserStateInterface` instead (has different methods but serves a similar purpose)               |
| `Converter`                    | Use `MarkdownConverter` instead.                                                                              |
| `ConverterInterface`           | Use `MarkdownConverterInterface`.  This interface has the same methods so it should be a drop-in replacement. |
| `UnmatchedBlockCloser`         | No longer needed 2.x                                                                                          |

## `EnvironmentInterface::HTML_INPUT_*` constants moved

The following constants have been moved:

| Old Location (1.x)                        | New Location (2.0)   |
| ----------------------------------------- | -------------------- |
| `EnvironmentInterface::HTML_INPUT_ALLOW`  | `HtmlFilter::ALLOW`  |
| `EnvironmentInterface::HTML_INPUT_ESCAPE` | `HtmlFilter::ESCAPE` |
| `EnvironmentInterface::HTML_INPUT_STRIP`  | `HtmlFilter::STRIP`  |

## Renamed Methods

The following methods have been renamed:

| Class                                              | Old Name (1.x)     | New Name (2.0)          |
| -------------------------------------------------- | ------------------ | ----------------------- |
| `Environment` / `ConfigurableEnvironmentInterface` | `addBlockParser()` | `addBlockStartParser()` |
| `ReferenceMap` / `ReferenceMapInterface`           | `addReference()`   | `add()`                 |
| `ReferenceMap` / `ReferenceMapInterface`           | `getReference()`   | `get()`                 |
| `ReferenceMap` / `ReferenceMapInterface`           | `listReferences()` | `getIterator()`         |

## Configuration Method Changes

Calling `EnvironmentInterface::getConfig()` or `ConfigurationInterface::get()` without any parameters is no longer supported.

Calling `ConfigurableEnvironmentInterface::mergeConfig()` without any parameters is no longer supported.

The `ConfigurableEnvironmentInterface::setConfig()` method has been removed.  Use `getConfig()` instead.

Calls to `ConfigurationInterface::set()` should always explicitly include the value being set.

## `bin/commonmark` command

This command was buggy to test and was relatively unpopular, so it has been removed. If you need this type of functionality, consider writing your own script with a Converter/Environment configured exactly how you want it.

## `CommonMarkConverter::VERSION` constant

This previously-deprecated constant was removed in 2.0 Use `\Composer\InstalledVersions` provided by composer-runtime-api instead.

## `HeadingPermalinkRenderer::DEFAULT_INNER_CONTENTS` constant

This previously-deprecated constant was removed in 2.0. Use `HeadingPermalinkRenderer::DEFAULT_SYMBOL` instead.
