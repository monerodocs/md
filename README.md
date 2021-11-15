# MoneroDocs

- [MoneroDocs](#monerodocs)
  - [About](#about)
  - [Contributing](#contributing)

## About

Monerodocs attempts to organize basic technical knowledge on Monero in one place.

While technical explanations are out there, knowledge is scattered through reddit posts, git comments, stack exchange answers, chat logs and the source code. This makes it hard to find complete and up-to-date explanations on advanced topics.

The goal is to educate and onboard power users faster.

## Contributing

To contribute to MoneroDocs, you'll need to setup your local [Mkdocs-Material](https://squidfunk.github.io/mkdocs-material/) environment:

```sh
# Fork the monerodocs/md Github repo

# Clone your newly created repo- be sure to include the submodule flag
git clone --recurse-submodules git@github.com:your-user-name/md.git

cd md

# Install mkdocs dependencies
pip install mkdocs-material mkdocs-minify-plugin

# Run the documentation server locally
python3 -m mkdocs serve
```
