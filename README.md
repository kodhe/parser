# CodeIgniter 3 Parser - Modular Refactoring

A template parser library for CodeIgniter 3, refactored into a modular structure while keeping the original API intact.

## Structure

```
Parser/
├── Parser.php                    # Main class (API CI3 compatible)
├── Contracts/
│   ├── ParserInterface.php       # Main parser interface
│   ├── LexerInterface.php        # Tokenizer interface
│   ├── CompilerInterface.php     # Compiler interface
│   ├── CacheInterface.php        # Cache interface
│   └── TokenInterface.php        # Token interface
├── Lexer/
│   └── TemplateLexer.php         # Template tokenizer
├── Compiler/
│   └── TemplateCompiler.php      # Interpreter-pattern compiler
├── Context/
│   └── ParseContext.php          # Context management
├── Factory/
│   └── ParserFactory.php         # Factory + Builder pattern
├── Cache/
│   └── TemplateCache.php         # Lazy compilation cache
├── Support/
│   └── TemplateHelper.php        # Utility functions
├── ValueObjects/
│   └── Token.php                 # Immutable token value object
├── tests/
│   └── ParserTest.php            # PHPUnit test suite
├── composer.json                 # PSR-4 autoloading
└── phpunit.xml                   # PHPUnit configuration
```

## API (100% CI3 Compatible)

```php
// Basic usage
$parser = new \Kodhe\Framework\Parser\Parser();

// Parse a template with variables
$template = 'Hello, {name}!';
$data = ['name' => 'World'];
echo $parser->parse($template, $data);

// Parse string (alias)
echo $parser->parse_string($template, $data);

// Custom delimiters
$parser->set_delimiters('[', ']');
```

## Features

### Patterns
- **Interpreter**: TemplateCompiler interprets tokens
- **Strategy**: Lexer, Compiler and Cache are swappable
- **Factory**: ParserFactory creates instances
- **Builder**: Fluent interface on ParserFactory
- **Dependency Injection**: Constructor injection in Parser

### Performance
- **Compiled template cache**: Compilation results are cached
- **Lazy compile**: The cache is checked before compiling
- **Token reuse**: Tokens can be recreated efficiently

### Testing
- Variable substitution
- Loop constructs
- Nested loops
- Conditional blocks
- Include directives
- Cache behavior
- Custom delimiters

## Installation

```bash
composer install
```

## Running Tests

```bash
vendor/bin/phpunit
```

## Usage Examples

### Variables
```php
$template = '{title} - {content}';
$data = ['title' => 'Hello', 'content' => 'World'];
$parser->parse($template, $data);
// Output: Hello - World
```

### Loops
```php
$template = '{loop items}<li>{items}</li>{/loop}';
$data = ['items' => ['A', 'B', 'C']];
$parser->parse($template, $data);
// Output: <li>A</li><li>B</li><li>C</li>
```

### Nested Loops
```php
$template = '{loop categories}{category}: {loop products}{products}, {/loop}{/loop}';
$data = [
    'categories' => [
        ['category' => 'Fruits', 'products' => ['Apple', 'Banana']],
        ['category' => 'Vegetables', 'products' => ['Carrot']]
    ]
];
```

### Conditionals
```php
$template = '{if show}Visible{/if}';
$data = ['show' => 'yes'];
$parser->parse($template, $data);

// Negation
$template = '{if !hidden}Visible{/if}';
$data = ['hidden' => ''];
```

### Includes
```php
$parser->setViewPaths(['/path/to/views']);
$template = '{include "header.html"}Content{include "footer.html"}';
```

### Custom Delimiters
```php
$parser->set_delimiters('{{', '}}');
$template = '{{greeting}}, {{name}}!';
```

### Factory Pattern
```php
use Kodhe\Framework\Parser\Factory\ParserFactory;

// Default
$parser = ParserFactory::make();

// With config
$parser = ParserFactory::makeWithConfig([
    'left_delimiter' => '{{',
    'right_delimiter' => '}}',
    'cache_enabled' => false,
    'view_paths' => ['/path/to/views']
]);

// With custom components
$parser = ParserFactory::makeWithComponents(
    new CustomLexer(),
    new CustomCompiler(),
    new CustomCache()
);
```

### Cache Control
```php
$parser->setCacheEnabled(false);
$parser->clearCache();
```

### Dot Notation
```php
$template = '{user.name} - {user.email}';
$data = ['user' => ['name' => 'John', 'email' => 'john@example.com']];
// Output: John - john@example.com
```

## PSR-4 Autoloading

```json
{
    "autoload": {
        "psr-4": {
            "CodeIgniter\\Parser\\": ""
        }
    }
}
```

## Requirements

- PHP >= 7.2
- PHPUnit >= 8.0 (for testing)

## License

MIT
