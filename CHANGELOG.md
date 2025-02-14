# Change Log
All notable changes to this project will be documented in this file.
Updates should follow the [Keep a CHANGELOG](https://keepachangelog.com/) principles.

## [Unreleased][unreleased]

See <https://commonmark.thephpleague.com/2.0/upgrading/> for detailed information on upgrading to version 2.0.

### Added

 - Added new `FrontMatterExtension` ([see documentation](https://commonmark.thephpleague.com/extensions/front-matter/))
 - Added new `DefaultAttributesExtension` ([see documentation](https://commonmark.thephpleague.com/extensions/default-attributes/))
 - Added `Query` class to simplify Node traversal when looking to take action on certain Nodes
 - Added the ability to delegate event dispatching to PSR-14 compliant event dispatcher libraries
 - Added the ability to configure disallowed raw HTML tags (#507)
 - Added the ability for Mentions to use multiple characters for their symbol (#514, #550)
 - Added `heading_permalink/min_heading_level` and `heading_permalink/max_heading_level` options to control which headings get permalinks (#519)
 - Added `footnote/backref_symbol` option for customizing backreference link appearance (#522)
 - Added new `HtmlFilter` and `StringContainerHelper` utility classes
 - Added new `AbstractBlockContinueParser` class to simplify the creation of custom block parsers
 - Added several new classes and interfaces:
   - `BlockContinue`
   - `BlockContinueParserInterface`
   - `BlockContinueParserWithInlinesInterface`
   - `BlockStart`
   - `BlockStartParserInterface`
   - `ChildNodeRendererInterface`
   - `ConfigurableExtensionInterface`
   - `ConfigurationBuilderInterface`
   - `ConfigurationProviderInterface`
   - `CursorState`
   - `DashParser` (extracted from `PunctuationParser`)
   - `DelimiterParser`
   - `DocumentBlockParser`
   - `DocumentRenderedEvent`
   - `EllipsesParser` (extracted from `PunctuationParser`)
   - `ExpressionInterface`
   - `HtmlRendererInterface`
   - `InlineParserEngineInterface`
   - `InlineParserMatch`
   - `MarkdownParserState`
   - `MarkdownParserStateInterface`
   - `MutableConfigurationInterface`
   - `Query`
   - `ReadOnlyConfiguration`
   - `ReferenceableInterface`
   - `RenderedContent`
   - `RenderedContentInterface`
   - `SpecReader`
 - Added several new methods:
   - `ConfigurationInterface::exists()`
   - `Environment::createDefaultConfiguration()`
   - `Environment::setEventDispatcher()`
   - `EnvironmentInterface::getExtensions()`
   - `EnvironmentInterface::getInlineParsers()`
   - `FencedCode::setInfo()`
   - `Heading::setLevel()`
   - `HtmlRenderer::renderDocument()`
   - `InlineParserContext::getFullMatch()`
   - `InlineParserContext::getFullMatchLength()`
   - `InlineParserContext::getMatches()`
   - `InlineParserContext::getSubMatches()`
   - `InvalidOptionException::forConfigOption()`
   - `InvalidOptionException::forParameter()`
   - `LinkParserHelper::parsePartialLinkLabel()`
   - `LinkParserHelper::parsePartialLinkTitle()`
   - `RegexHelper::isLetter()`
   - `StringContainerInterface::setLiteral()`
   - `TableCell::getType()`
   - `TableCell::setType()`
   - `TableCell::getAlign()`
   - `TableCell::setAlign()`
 - Added purity markers throughout the codebase (verified with Psalm)

### Changed

 - `CommonMarkConverter::convertToHtml()` now returns an instance of `RenderedContentInterface`. This can be cast to a string for backward compatibility with 1.x.
 - Table of Contents items are no longer wrapped with `<p>` tags (#613)
 - Changes to configuration options:
     - `enable_em` has been renamed to `commonmark/enable_em`
     - `enable_strong` has been renamed to `commonmark/enable_strong`
     - `use_asterisk` has been renamed to `commonmark/use_asterisk`
     - `use_underscore` has been renamed to `commonmark/use_underscore`
     - `unordered_list_markers` has been renamed to `commonmark/unordered_list_markers`
     - `mentions/*/symbol` has been renamed to `mentions/*/prefix`
     - `mentions/*/regex` has been renamed to `mentions/*/pattern` and requires partial regular expressions (without delimiters or flags)
     - `max_nesting_level` now defaults to `PHP_INT_MAX` and no longer supports floats
 - Event dispatching is now fully PSR-14 compliant
 - The `AbstractBlock::$data` and `AbstractInline::$data` arrays were replaced with a `Data` array-like object on the base `Node` class
 - Moved and renamed several classes - [see the full list here](https://commonmark.thephpleague.com/2.0/upgrading/#classesnamespaces-renamed)
 - Implemented a new approach to block parsing. This was a massive change, so here are the highlights:
   - Functionality previously found in block parsers and node elements has moved to block parser factories and block parsers, respectively ([more details](https://commonmark.thephpleague.com/2.0/upgrading/#new-block-parsing-approach))
   - `ConfigurableEnvironmentInterface::addBlockParser()` is now `EnvironmentBuilderInterface::addBlockParserFactory()`
   - `ReferenceParser` was re-implemented and works completely different than before
   - The paragraph parser no longer needs to be added manually to the environment
 - Implemented a new approach to inline parsing where parsers can now specify longer strings or regular expressions they want to parse (instead of just single characters):
   - `InlineParserInterface::getCharacters()` is now `getMatchDefinition()` and returns an instance of `InlineParserMatch`
   - `InlineParserContext::__construct()` now requires the contents to be provided as a `Cursor` instead of a `string`
 - Implemented delimiter parsing as a special type of inline parser (via the new `DelimiterParser` class)
 - Changed block and inline rendering to use common methods and interfaces
   - `BlockRendererInterface` and `InlineRendererInterface` were replaced by `NodeRendererInterface` with slightly different parameters. All core renderers now implement this interface.
   - `ConfigurableEnvironmentInterface::addBlockRenderer()` and `addInlineRenderer()` were combined into `EnvironmentBuilderInterface::addRenderer()`
   - `EnvironmentInterface::getBlockRenderersForClass()` and `getInlineRenderersForClass()` are now just `getRenderersForClass()`
 - Completely refactored the Configuration implementation
   - `Configuration` object must now be configured with a schema and all options must match that schema - arbitrary keys are no longer permitted
   - `Configuration::__construct()` no longer accepts the default configuration values - use `Configuration::merge()` instead
   - `ConfigurationInterface` now only contains a `get(string $key)`; this method no longer allows arbitrary default values to be returned if the option is missing
   - `ConfigurableEnvironmentInterface` was renamed to `EnvironmentBuilderInterface`
   - `ExtensionInterface::register()` now requires an `EnvironmentBuilderInterface` param instead of `ConfigurableEnvironmentInterface`
 - Re-implemented the GFM Autolink extension using the new inline parser approach instead of document processors
   - `EmailAutolinkProcessor` is now `EmailAutolinkParser`
   - `UrlAutolinkProcessor` is now `UrlAutolinkParser`
 - `HtmlElement` can now properly handle array (i.e. `class`) and boolean (i.e. `checked`) attribute values
 - `HtmlElement` automatically flattens any attributes with array values into space-separated strings, removing duplicate entries
 - Combined separate classes/interfaces into one:
   - `DisallowedRawHtmlRenderer` replaces `DisallowedRawHtmlBlockRenderer` and `DisallowedRawHtmlInlineRenderer`
   - `NodeRendererInterface` replaces `BlockRendererInterface` and `InlineRendererInterface`
 - Renamed the following methods:
   - `Environment` and `ConfigurableEnvironmentInterface`:
     - `addBlockParser()` is now `addBlockStartParser()`
   - `ReferenceMap` and `ReferenceMapInterface`:
     - `addReference()` is now `add()`
     - `getReference()` is now `get()`
     - `listReferences()` is now `getIterator()`
   - Various node (block/inline) classes:
     - `getContent()` is now `getLiteral()`
     - `setContent()` is now `setLiteral()`
 - Moved and renamed the following constants:
   - `EnvironmentInterface::HTML_INPUT_ALLOW` is now `HtmlFilter::ALLOW`
   - `EnvironmentInterface::HTML_INPUT_ESCAPE` is now `HtmlFilter::ESCAPE`
   - `EnvironmentInterface::HTML_INPUT_STRIP` is now `HtmlFilter::STRIP`
 - Changed the visibility of the following properties:
   - `TableCell::$align` is now `private`
   - `TableCell::$type` is now `private`
   - `TableSection::$type` is now `private`
 - Added missing return types to virtually every class and interface method
 - Several methods which previously returned `$this` now return `void`
   - `Delimiter::setPrevious()`
   - `Node::replaceChildren()`
   - `Context::setTip()`
   - `Context::setContainer()`
   - `Context::setBlocksParsed()`
   - `AbstractStringContainer::setContent()`
   - `AbstractWebResource::setUrl()`
 - `Heading` nodes no longer directly contain a copy of their inner text
 - `StringContainerInterface` can now be used for inlines, not just blocks
 - `ArrayCollection` is now final and only supports integer keys
 - `Cursor::saveState()` and `Cursor::restoreState()` now use `CursorState` objects instead of arrays
 - `NodeWalker::next()` now enters, traverses any children, and leaves all elements which may have children (basically all blocks plus any inlines with children). Previously, it only did this for elements explicitly marked as "containers".
 - `InvalidOptionException` was renamed to `InvalidConfigurationException` and now extends from `UnexpectedValueException`
 - Anything with a `getReference(): ReferenceInterface` method now implements `ReferencableInterface`
 - Several changes made to the Footnote extension:
   - Footnote identifiers can no longer contain spaces
   - Anonymous footnotes can now span subsequent lines
   - Footnotes can now contain multiple lines of content, including sub-blocks, by indenting them
   - Footnote event listeners now have numbered priorities (but still execute in the same order)
   - Footnotes must now be separated from previous content by a blank line
 - The line numbers (keys) returned via `MarkdownInput::getLines()` now start at 1 instead of 0
 - `DelimiterProcessorCollectionInterface` now extends `Countable`
 - `RegexHelper::PARTIAL_` constants must always be used in case-insensitive contexts
 - `HeadingPermalinkProcessor` no longer accepts text normalizers via the constructor - these must be provided via configuration instead
 - Block which can't contain inlines will no longer be asked to render inlines

### Fixed

 - Fixed parsing of footnotes without content
 - Fixed rendering of orphaned footnotes and footnote refs
 - Fixed some URL autolinks breaking too early (#492)

### Removed

 - Removed support for PHP 7.1
 - Removed all previously-deprecated functionality:
   - Removed the `Converter` class and `ConverterInterface`
   - Removed the `bin/commonmark` script
   - Removed the `Html5Entities` utility class
   - Removed the `InlineMentionParser` (use `MentionParser` instead)
   - Removed `DefaultSlugGenerator` and `SlugGeneratorInterface` from the `Extension/HeadingPermalink/Slug` sub-namespace (use the new ones under `./SlugGenerator` instead)
   - Removed the following `ArrayCollection` methods:
     - `add()`
     - `set()`
     - `get()`
     - `remove()`
     - `isEmpty()`
     - `contains()`
     - `indexOf()`
     - `containsKey()`
     - `replaceWith()`
     - `removeGaps()`
   - Removed the `ConfigurableEnvironmentInterface::setConfig()` method
   - Removed the `ListBlock::TYPE_UNORDERED` constant
   - Removed the `CommonMarkConverter::VERSION` constant
   - Removed the `HeadingPermalinkRenderer::DEFAULT_INNER_CONTENTS` constant
   - Removed the `heading_permalink/inner_contents` configuration option
 - Removed now-unused classes:
   - `AbstractStringContainerBlock`
   - `BlockRendererInterface`
   - `Context`
   - `ContextInterface`
   - `Converter`
   - `ConverterInterface`
   - `InlineRendererInterface`
   - `PunctuationParser` (was split into two classes: `DashParser` and `EllipsesParser`)
   - `UnmatchedBlockCloser`
 - Removed the following methods, properties, and constants:
   - `AbstractBlock::$open`
   - `AbstractBlock::$lastLineBlank`
   - `AbstractBlock::isContainer()`
   - `AbstractBlock::canContain()`
   - `AbstractBlock::isCode()`
   - `AbstractBlock::matchesNextLine()`
   - `AbstractBlock::endsWithBlankLine()`
   - `AbstractBlock::setLastLineBlank()`
   - `AbstractBlock::shouldLastLineBeBlank()`
   - `AbstractBlock::isOpen()`
   - `AbstractBlock::finalize()`
   - `AbstractBlock::getData()`
   - `AbstractInline::getData()`
   - `ConfigurableEnvironmentInterface::addBlockParser()`
   - `ConfigurableEnvironmentInterface::mergeConfig()`
   - `ConfigurationInterface::merge()` (moved to new `MutableConfigurationInterface`)
   - `ConfigurationInterface::replace()`
   - `ConfigurationInterface::set()` (moved to new `MutableConfigurationInterface`)
   - `Delimiter::setCanClose()`
   - `EnvironmentInterface::getConfig()`
   - `EnvironmentInterface::getInlineParsersForCharacter()`
   - `EnvironmentInterface::getInlineParserCharacterRegex()`
   - `HtmlRenderer::renderBlock()`
   - `HtmlRenderer::renderBlocks()`
   - `HtmlRenderer::renderInline()`
   - `HtmlRenderer::renderInlines()`
   - `Node::isContainer()`
   - `RegexHelper::matchAll()` (use the new `matchFirst()` method instead)
   - `RegexHelper::REGEX_WHITESPACE`
 - Removed the second `$contents` argument from the `Heading` constructor

### Deprecated

The following things have been deprecated and will not be supported in v3.0:

 - `Environment::mergeConfig()` (set configuration before instantiation instead)
 - Instantiating `CommonMarkConverter` or `GithubFlavoredMarkdownConverter` with custom environments (use `MarkdownConverter` instead)
 - `Environment::createCommonMarkEnvironment()` and `Environment::createGFMEnvironment()`
    - Alternative 1: Use `CommonMarkConverter` or `GithubFlavoredMarkdownConverter` if you don't need to customize the environment
    - Alternative 2: Instantiate a new `Environment` and add the necessary extensions yourself

[unreleased]: https://github.com/thephpleague/commonmark/compare/1.6...latest
