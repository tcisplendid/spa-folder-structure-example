# A SPA folder structure example
The SPA folder structures I have seen or found online are often organized in the separation of technical concern. The top directories usually consist of components, hooks, api, utils and so on. It doesn't scale for large codebase. One may have to open many nested folders to modify multiple files for a small feature.
React claims that it's a library, not a framework, but it indeed fosters a framework-like ecosystem. The code concerning React first instead of data/application first often violates the rule that view should depend on model. React is a view library. Hooks belong to view layer, which shouldn't be a solution to model layer problem. Yet a certain number of libraries and articles consider hook is a good way to solve business logic related problems. Those hints prove a certain number of React developers work in a view-driven way, favoring React and component over data and application logic. 
This example wants to re-emphasis the lesson from clean architecture and domain-driven development(DDD): code should be organized around application which is independent of all libraries, frameworks, tools, databases and other technologies. However, this is not an adaption of clean architecture or DDD in React world. I feel they seem an overkill for most front-end projects. Even VSCode's source code doesn't contain so many layers.

## Structure
```
./src
|-core
    |-auth
    |-router
    |-stateManager
    // other functionalities independent of specific modules
	|-App.tsx
|-common
	|-constants
		/**
		 * Can't think of good use case for this folder.
		 * Constants can be categorized as:
		 * - configs. They should be where they are needed or related to, such the app config or module configs. If you have to gather all configs, a folder called 'configs' is much better than 'constants'.
		 * - model definitions, such as enums. should be in the 'model' folder.
		 * - solutions to magic number/string. Just put them where magic numbers/strings are used. It's very hard to come up with a case that a magic number is used across the whole app while it is not a config or related to a model.
		 *
		 * DON't put anything here except you have very strong reasons.
		 */
	|-features // this 'features' folder is for cohesion of different kinds of code around some specific feature, e.g. a searchList.tsx consisting of all the components, utils, hooks etc. for pagination.
	|-components
	|-hooks
	|-model // This 'model' folder is for common model definitions including constants and typings, as well as methods related to correspond models. It can also be called 'data'.
	|-service
	|-snippets // This 'snippets' folder is for copy-and-pasting. Certain code is easier for understanding and customizing in the form of source code rather than encapsulation. This would be a good source for code templates.
	|-typings // This folder is for generic utility types. DON'T separate types with their origins. It's fine and actually better to export functions/constants/components/hooks with their types.
	|-utils
|-modules
	|-module1
		|-model
		|-pages
			|-index.tsx
            |-utils.ts
            |-hooks.ts
			|-Children
				|-index.tsx
                |-utils.ts
                |-hooks.ts					
	|-module2
```
The top-level directories are `core`, `common` and `modules`. 
* `core` is what the app needs to bootstrap, including what `infra`-like folders include. It might depend on some general code from `common`, but independent of `modules`. Inside `core`, code are separated by feature.
* `common` ≈ general ≈ what you can put on npm. It is not defined by how many times the code is used across the app. It might depend on some code from `core`, but independent of `modules`. Inside `core`, code are separated by technical concerns.
    * Code automatically generated in the granularity of app can be put in `common`.  
    * Even when a module is referenced frequently, it shouldn't be put in `common`. It just shows the interaction of modules. The proximity between modules is a sign you may consider merging two modules.
* `modules` contains domain-specific code. Each module is layered with `model`, `pages` and whatever layer you need for MVX pattern. They depend on `core` and `common`.
    * `pages` are fractal, meaning its children can have the exact same shape as itself. They can be organized in any way as long as it doesn't contain `model` code.

Each top-level directory's sub-folders can be lifted up to top-level for the sake of less nestedness. `modules` is recommanded to be lifted at higher priority, because developers usually open those files the most and those folders tell what the app is the best.