# API: `Plugins`

Plugins allow you to extend the interface of each context type.

## Creating Plugins

Here, we'll give components access to the `getFoo()` function:

```js
import Fluxible from 'fluxible';
let app = new Fluxible();

app.plug({
    // Required unique name property
    name: 'TestPlugin',
    
    /**
     * Called after context creation to dynamically create a context plugin
     * @method plugContext
     * @param {Object} options Options passed into createContext
     * @param {Object} context FluxibleContext instance
     * @param {Object} app Fluxible instance
     */
    plugContext: function (options, context, app) {
        // `options` is the same as what is passed into `Fluxible.createContext(options)`
        let foo = options.foo;
        
        // Returns a context plugin
        return {
            /**
             * Method called to allow modification of the component context
             * @method plugComponentContext
             * @param {Object} componentContext Options passed into createContext
             * @param {Object} context FluxibleContext instance
             * @param {Object} app Fluxible instance
             */
            plugComponentContext: function (componentContext, context, app) {
                componentContext.getFoo = function () {
                    return foo;
                };
            },
            //plugActionContext: function (actionContext, context, app) {}
            //plugStoreContext: function (storeContext, context, app) {}

            /**
             * Allows context plugin settings to be persisted between server and client. Called on server
             * to send data down to the client
             * @method dehydrate
             */
            dehydrate: function () {
                return {
                    foo: foo
                };
            },

            /**
             * Called on client to rehydrate the context plugin settings
             * @method rehydrate
             * @param {Object} state Object to rehydrate state
             */
            rehydrate: function (state) {
                foo = state.foo;
            }
        };
    },

    /**
     * Allows dehydration of application plugin settings
     * @method dehydrate
     */
    dehydrate: function () { return {}; },

    /**
     * Allows rehydration of application plugin settings
     * @method rehydrate
     * @param {Object} state Object to rehydrate state
     */
    rehydrate: function (state) {}
});

let context = app.createContext({
    foo: 'bar'
});

context.getComponentContext().getFoo(); // returns 'bar'
// or this.context.getFoo() from a React component
```

_When add to the component context, you will need to add the new properties to 
`childContextTypes` so that it is made available to all children. 
`provideContext` has an option for 
[passing custom contextTypes](http://fluxible.io/addons/provideContext.html#plugins-and-custom-component-context)._

Example plugins:
 * [fluxible-plugin-fetchr](https://github.com/yahoo/fluxible-plugin-fetchr) - Polymorphic RESTful services
 * [Search for more on npm](https://www.npmjs.com/search?q=fluxible+plugin)
