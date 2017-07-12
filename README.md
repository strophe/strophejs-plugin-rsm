# strophe.rsm.js

Plugin for [strophe.js](https://www.npmjs.com/package/strophe.js) providing Result Set Management (RSM) [XEP-0059](http://xmpp.org/extensions/xep-0059.html).

## Install

```bash
npm install strophejs-plugin-rsm
```

### ES6

```js
import 'strophejs-plugin-rsm';
```

### Browser

``` html
<script src="strophe.min.js"></script>
<script src="strophe.rsm.js"></script>
```

## Usage

Make sure [Strophe](https://www.npmjs.com/package/strophe.js) is installed and the plugin is imported into your app. With RSM is available to do paged stanza requests like so:

```js
require('strophejs-plugin-rsm');

var connection = new Strophe.Connection('wss://...');

var options = {
  before: '',
  isGroup: false,
  max: 50,
};

var messages = [];

var queryid = connection.getUniqueId();
var stanza = $iq({ type: 'set' }).c('query', { xmlns: Strophe.NS.MAM, queryid });

var node = new Strophe.RSM(options);

stanza.cnode(node.toXML());

connection.addHandler((message: any) => {
  var fin = getElementByTagName(message, 'fin', Strophe.NS.MAM);
  var result = getElementByTagName(message, 'result', Strophe.NS.MAM);

  if (fin && queryid === fin.getAttribute('queryid')) {
    var paging = new Strophe.RSM({ xml: fin });

    // `paging` is now our paged RSM object with the following attributes:
    //    paging.next()
    //    paging.previous()
    //    paging.toXML()
    //    paging.fromXMLElement()

    return false;
  } else if (result && queryid === result.getAttribute('queryid')) {
    messages.push(message);
  }

  return true;
}, Strophe.NS.MAM);

connection.sendIQ(stanza, null, null);
```
