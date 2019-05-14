---
title: Bellona Documentation

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - directory-structure
  - architecture
  - routing
  - middleware
  - authorization
  - controllers
  - models
  - views
  - csrf-protection
  - form-validation
  - file-uploads
  - query-builder

search: true
---

# Introduction

Welcome to the official documentation for Bellona, an open-source PHP framework inspired by Laravel!

These documents are supposed to introduce many of the key concepts and provide examples showing you how to use Bellona in your own application. However, they do not introduce everything that Bellona has to offer; many other services and functions go undocumented here, but you can find more details at the full [Bellona API reference](#) (coming soon...).

## Requirements

- PHP >= 7.1
- mod_rewrite Apache module enabled
- PDO PHP extension
- OpenSSL PHP extension
- JSON PHP extension

## Installation

### Via `composer create-project`

1. Create project with `composer create-project`:<br>
   `composer create-project jlwalker/bellona my-project`

### Via `git clone`

1. Clone the Bellona project directory to quickly setup your project structure:<br>
   `git clone https://github.com/jlwalkerlg/bellona.git my-project`<br>
   `cd my-project`
2. Install all the required packages through composer:<br>
   `composer install`

<aside class="notice">
  Since Bellona requires PHP >= 7.1, composer will throw an error if your system does not match this requirement when installing the framework. If you are using Docker, you will want to tell composer to ignore this requirement by adding the <code>--ignore-platform-reqs</code> flag.
</aside>

## Setup

1. Rename the `.env.example` file to `.env`.
2. Configure Bellona by editing the `.env` file and the `app/config` files where appropriate.

**That's it!**

## Development

### Docker

If you are using Docker, Bellona comes with a `Dockerfile` and a `docker-compose.yml` file for serving your application. Run `docker-compose up -d` after setup to serve your application to port 80, and [Adminer](https://www.adminer.org/) on port 8080. You can seed your database by adding `SQL` to the `database/seed.sql` file.
