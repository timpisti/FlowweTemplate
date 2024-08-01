# TemplateService for Web Components

TemplateService is a lightweight, efficient templating system designed for use with Web Components. It provides a powerful way to create dynamic, data-driven templates with a syntax reminiscent of popular frameworks like Angular or Vue.js, but implemented in vanilla JavaScript.

## Features

- Dynamic content rendering based on data context
- Support for loops and conditional statements within templates
- Event handling and binding to component methods
- Rerendering capability for updating component content
- Safe(r) evaluation of expressions


- Working standalone or a part of [Flowwe JS](https://github.com/timpisti/Flowwe-JS) single-page app as a template engine
- Working together with FlowweTranslate - a simple translator service

## Installation

Clone this repository or copy the `TemplateService.js` file into your project:

```bash
git clone https://github.com/timpisti/FlowweTemplate
```
## Usage

### Basic Setup

1. Import the TemplateService in your Web Component:

```
import { TemplateService } from './TemplateService.js';

class MyComponent extends HTMLElement {
    constructor() {
        super();
        this.templateService = new TemplateService();
        this.attachShadow({ mode: 'open' });
    }
    // ...
}
```

2. Define your template:

```
this.template = `
    <h1>{{title}}</h1>
    <ul>
        @for(item of items)
            <li>{{item.name}}</li>
        @endfor
    </ul>
    <button (click)="handleClick()">Click me</button>
`;
```

3. Render the template:

```
connectedCallback() {
    const data = {
        title: 'My List',
        items: [
          { name: 'Item 1' },
            { name: 'Item 2' },
            { name: 'Item 3' }
        ]
    };
    const content = this.templateService.processTemplate(this.template, this, data);
    this.shadowRoot.appendChild(content);
}
```

OR Imoprt it your main page

```
<script src="your_path/templateService.js" defer></script>
```

In this case the example:
```
class YourComponent extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({ mode: 'open' });
        this.data = {}; // Initialize your data
    }

    connectedCallback() {
        // Ensure templateService exists
        if (!window.templateService) {
            console.error('TemplateService not found. Ensure it is loaded before components.');
            return;
        }

        this.templateService = window.templateService;
        
        // Load your template text (e.g., from an attribute or external file)
        this.template = this.getAttribute('template') || ''; // Example: load from attribute

        // Initial render
        this.render();
    }

    render() {
        if (this.templateService && this.template) {
            const content = this.templateService.processTemplate(this.template, this, this.data);
            this.shadowRoot.innerHTML = ''; // Clear existing content
            this.shadowRoot.appendChild(content);
        }
    }

    // Example method to update data and re-render
    updateData(newData) {
        this.data = { ...this.data, ...newData };
        this.render();
    }
}

customElements.define('your-component', YourComponent);
```


## Template Syntax

### Data Binding

Use double curly braces for simple data binding:

```
<p>Hello, {{name}}!</p>
```

### Loops
Use @for directive for loops:

```
@for(item of items)
    <li>{{item.name}} costs ${{item.price}}</li>
@endfor
```

### Conditionals

Use @if, @else, and @endif for conditional rendering:

```
@if(user.isLoggedIn)
    <button>Logout</button>
@else
    <button>Login</button>
@endif
```

### Event-binding

Bind events to component methods:

```
<button (click)="handleClick()">Click me</button>
```

### Rendering

To update the component's content:

```
updateData(newData) {
    this.templateService.rerenderTemplate(this, newData);
}
```

### Advanced Example
Here's a more complex example showcasing various features:

```
import { TemplateService } from './TemplateService.js';

class ProductList extends HTMLElement {
    constructor() {
        super();
        this.templateService = new TemplateService();
        this.attachShadow({ mode: 'open' });
        this.template = `
            <style>
                .product { border: 1px solid #ddd; margin: 10px; padding: 10px; }
                .out-of-stock { color: red; }
            </style>
            <h1>{{title}}</h1>
            @if(products.length > 0)
                @for(product of products)
                    <div class="product">
                        <h2>{{product.name}}</h2>
                        <p>Price: ${{product.price}}</p>
                        @if(product.stock > 0)
                            <button (click)="addToCart({{product.id}})">Add to Cart</button>
                        @else
                            <p class="out-of-stock">Out of Stock</p>
                        @endif
                    </div>
                @endfor
            @else
                <p>No products available.</p>
            @endif
        `;
    }

    connectedCallback() {
        this.render();
    }

    render() {
        const data = {
            title: 'Our Products',
            products: [
                { id: 1, name: 'Widget', price: 9.99, stock: 5 },
                { id: 2, name: 'Gadget', price: 24.99, stock: 0 },
                { id: 3, name: 'Doohickey', price: 14.99, stock: 3 }
            ]
        };
        const content = this.templateService.processTemplate(this.template, this, data);
        this.shadowRoot.appendChild(content);
    }

    addToCart(productId) {
        console.log(`Added product ${productId} to cart`);
        // Implement your cart logic here
    }
}

customElements.define('product-list', ProductList);
```
This example demonstrates a product list with conditional rendering, loops, event binding, and dynamic styling.
## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
## License
This project is licensed under the MIT License.
