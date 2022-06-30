----
title: PHP 代码风格检测/修复工具：Laravel Pint
date: 2022-06-30 15:06:00
categories: [编程,工具]
----

## 简介

- Github 地址：[https://github.com/laravel/pint](https://github.com/laravel/pint)
- Laravel Pint  是 Laravel 官方出品，是一个专为极简主义者设计的 PHP 代码风格修复器。
- 配置兼容 [php-cs-fixer](https://cs.symfony.com/)
- 支持 Laravel 与非 Laravel 项目

<!-- more -->

## 安装

- 安装 pint

```bash
composer require laravel/pint --dev
```

- 配置 `composer.json` `script`

```json
{
    "scripts": {
        "pint-test": [
            "./vendor/bin/pint --test -v"
        ],
        "pint-fix": [
            "./vendor/bin/pint -v"
        ]
}
```

- 运行

```bash
# 原始命令
./vendor/bin/pint --test -v # 运行语法检测（不修复） -v：查看明细
./vendor/bin/pint -v # 运行语法修复

# composer 方式
composer pint-test
composer pint-fix
```

## 附录
### 性能比对

**Pint 执行速度比 php-cs-fixer 更快**，如图示：

> 在无文件需要进行格式化的耗时，**php-cs-fixer 是 pint 的约3倍耗时**。

![](https://s.flc.io/2022-06-30-15-13-46.png)

### 配置相关

`pint` 配置兼容 `php-cs-fixer`，如下文 `pint` 的配置基本为 `.php-cs-fixer.php` JSON 版。

- `.php-cs-fixer.php`
```php
<?php

declare(strict_types=1);

return (new PhpCsFixer\Config())
    ->setRiskyAllowed(true)
    ->setRules([
        '@Symfony'                          => true,
        'array_syntax'                      => ['syntax' => 'short'],
        'ordered_imports'                   => true,
        'no_useless_else'                   => true,
        'no_useless_return'                 => true,
        'php_unit_construct'                => true,
        'php_unit_strict'                   => true,
        'yoda_style'                        => false,
        'phpdoc_summary'                    => false,
        'phpdoc_no_empty_return'            => false,
        'not_operator_with_successor_space' => true,
        'no_superfluous_phpdoc_tags'        => false,
        'binary_operator_spaces'            => ['default' => 'align_single_space'], // 等号对齐、数字箭头符号对齐
        'single_trait_insert_per_statement' => false,
        'blank_line_before_statement'       => ['statements' => ['declare']],
    ])
    ->setFinder(
        PhpCsFixer\Finder::create()
            ->exclude('_*')
            ->exclude('bin')
            ->exclude('bootstrap')
            ->exclude('public')
            ->exclude('vendor')
            ->exclude('storage')
            ->exclude('resources')
            ->exclude('public')
            ->in(__DIR__)
    )->setUsingCache(false);
```

- `pint.json`

```json
{
    "preset": "laravel",
    "rules": {
        "array_syntax": {
            "syntax": "short"
        },
        "ordered_imports": true,
        "no_useless_else" : true,
        "no_useless_return": true,
        "php_unit_construct": true,
        "php_unit_strict": true,
        "yoda_style": false,
        "phpdoc_summary": false,
        "phpdoc_no_empty_return": false,
        "not_operator_with_successor_space": true,
        "no_superfluous_phpdoc_tags": false,
        "binary_operator_spaces": {
            "default": "align_single_space"
        },
        "single_trait_insert_per_statement": false,
        "blank_line_before_statement": {
            "statements": ["declare"]
        }
    },
    "exclude": [
        "storage",
        "resources",
        "public"
    ]
}
```