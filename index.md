# Building a Decentralized Blockchain

Liz Garcia
Full Stack Solutions Architect

[lizgarseeyah@gmail.com](lizgarseeyah@gmail.com)

[LinkedIn](https://www.linkedin.com/in/lizgarseeyah/)

[GitHub](https://www.github.com/lizgarseeyah/)

This project walks through the steps required to create a simple, immutable, decentralized blockchain using Javascript. The blockchain will be encrypted using the SHA256 algorithm. This program will also enable UI interaction with the blockchain using an HTML/CSS webapp called Block Explorer. Postman API app was used to send commands to the endpoints. This program allows the user to send transactions,  mine blocks, retrieve data, and broadcast nodes.

### Topics covered:
- Part I: Creating the Blockchain Data Structure
- Part II: Building out the API Endpoints
- Part III: Creating a Decentralized Network
- Part IV: Blockchain UI via Block Explorer

### Prerequisites (based on Mac OS 10.15.6):
- Node.js: terminal=>`brew install node` (also installs npm, requires Homebrew on system)
  - v14.14.0
- Express JS Library: terminal=> `npm i express`
- Body Parser: terminal=>`npm i body-parser --save`
- Request Promise Library: terminal => `npm install request-promise --save`
- [Postman App (v7.31.0)](https://www.postman.com/downloads/)
- [JSON Formatter(optional)](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en)

## Part I: Creating the Blockchain

### Simple Blockchain Constructor Function:

The data structure of the Blockchain is created from a series of Javascript constructors. The first step is to create an empty blockchain which is simply an array that stores our values:

```markdown
`function Blockchain() {
   this.chain = [];
   this.pendingTransactions = [];
};`
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/lizgarseeyah/Blockchain/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```
