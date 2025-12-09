# API Documentation Exercise

## Selected API Endpoint

I chose to document the JavaScript/Express Product API endpoints, specifically focusing on the GET endpoints for retrieving products. These endpoints provide good examples of filtering, pagination, and error handling that are common in real-world APIs.

## Prompt 1 Application: Comprehensive Endpoint Documentation

For the first endpoint, I applied Prompt 1 to generate detailed documentation:

### GET /api/products - List Products with Filtering and Pagination

**Purpose**: This endpoint retrieves a paginated list of products with flexible filtering options. It allows developers to search through products by category, price range, stock status, and provides sorting capabilities.

**Request Parameters**:

Query Parameters:
- category (string, optional) - Filters products by specific category
- minPrice (number, optional) - Sets minimum price threshold for filtering
- maxPrice (number, optional) - Sets maximum price threshold for filtering  
- sort (string, optional) - Specifies field to sort by, defaults to 'createdAt'
- order (string, optional) - Sort direction, accepts 'asc' or 'desc', defaults to 'desc'
- page (integer, optional) - Page number for pagination, defaults to 1
- limit (integer, optional) - Number of items per page, defaults to 20, maximum 100
- inStock (boolean, optional) - When set to 'true', returns only products with available stock

**Response Format**:

Success Response (200 OK):
json
{
  "products": [
    {
      "_id": "61fa9bcf5c130b2e6d675432",
      "name": "Wireless Headphones",
      "description": "High-quality wireless headphones with noise cancellation",
      "price": 89.99,
      "category": "electronics",
      "stockQuantity": 45,
      "createdAt": "2023-02-01T15:32:47Z",
      "updatedAt": "2023-03-15T09:21:08Z"
    }
  ],
  "pagination": {
    "total": 42,
    "page": 1,
    "limit": 20,
    "pages": 3
  }
}


**Authentication Requirements**: No authentication required for this endpoint.

**Error Responses**:
- 500 Internal Server Error - Server processing error
  json
  {
    "error": "Server error",
    "message": "Failed to fetch products"
  }
  

**Example Requests**:

1. Basic product listing:
   
   GET /api/products
   

2. Filtered by category with pagination:
   
   GET /api/products?category=electronics&page=1&limit=10
   

### GET /api/products/:productId - Get Single Product

**Purpose**: Retrieves detailed information for a specific product using its unique identifier.

**Request Parameters**:

Path Parameters:
- productId (string, required) - MongoDB ObjectId of the product

**Response Format**:

Success Response (200 OK):
json
{
  "_id": "61fa9bcf5c130b2e6d675432",
  "name": "Wireless Headphones",
  "description": "High-quality wireless headphones with noise cancellation",
  "price": 89.99,
  "category": "electronics",
  "stockQuantity": 45,
  "createdAt": "2023-02-01T15:32:47Z",
  "updatedAt": "2023-03-15T09:21:08Z"
}


**Error Responses**:
- 400 Bad Request - Invalid product ID format
- 404 Not Found - Product not found
- 500 Internal Server Error - Server processing error

**Example Request**:

GET /api/products/61fa9bcf5c130b2e6d675432


## Prompt 2 Application: OpenAPI/Swagger Conversion

I converted the documentation into OpenAPI 3.0 format for better tooling integration:

yaml
openapi: 3.0.0
info:
  title: Product Management API
  description: Comprehensive API for product catalog management
  version: 1.0.0
servers:
  - url: https://api.example.com
paths:
  /api/products:
    get:
      summary: List products with filtering
      description: Retrieve paginated product list with flexible filtering and sorting options
      parameters:
        - name: category
          in: query
          schema:
            type: string
          description: Filter by product category
        - name: minPrice
          in: query
          schema:
            type: number
            minimum: 0
          description: Minimum price filter
        - name: maxPrice
          in: query
          schema:
            type: number
            minimum: 0
          description: Maximum price filter
        - name: sort
          in: query
          schema:
            type: string
            default: createdAt
          description: Field to sort by
        - name: order
          in: query
          schema:
            type: string
            enum: [asc, desc]
            default: desc
          description: Sort order
        - name: page
          in: query
          schema:
            type: integer
            minimum: 1
            default: 1
          description: Page number
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
          description: Items per page
        - name: inStock
          in: query
          schema:
            type: boolean
          description: Filter for in-stock items only
      responses:
        '200':
          description: Products retrieved successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  products:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
                  pagination:
                    $ref: '#/components/schemas/PaginationInfo'
        '500':
          description: Server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
  /api/products/{productId}:
    get:
      summary: Get product by ID
      description: Retrieve detailed information for a specific product
      parameters:
        - name: productId
          in: path
          required: true
          schema:
            type: string
          description: Product identifier
      responses:
        '200':
          description: Product found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '400':
          description: Invalid product ID format
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '404':
          description: Product not found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'
        '500':
          description: Server error
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

components:
  schemas:
    Product:
      type: object
      properties:
        _id:
          type: string
          description: Unique product identifier
        name:
          type: string
          description: Product name
        description:
          type: string
          description: Product description
        price:
          type: number
          format: float
          description: Product price
        category:
          type: string
          description: Product category
        stockQuantity:
          type: integer
          description: Available stock quantity
        createdAt:
          type: string
          format: date-time
          description: Creation timestamp
        updatedAt:
          type: string
          format: date-time
          description: Last update timestamp
    PaginationInfo:
      type: object
      properties:
        total:
          type: integer
          description: Total number of items
        page:
          type: integer
          description: Current page number
        limit:
          type: integer
          description: Items per page
        pages:
          type: integer
          description: Total number of pages
    ErrorResponse:
      type: object
      properties:
        error:
          type: string
          description: Error type identifier
        message:
          type: string
          description: Human-readable error message


## Prompt 3 Application: Developer Usage Guide

I created a comprehensive developer guide targeting intermediate developers:

## Product API Developer Guide

### Getting Started with the Product API

This guide will help you integrate with our Product API endpoints to retrieve product information for your application.

#### Authentication Setup

Currently, the product retrieval endpoints do not require authentication. You can make direct requests to the API without additional headers or tokens.

#### Making Your First Request

The simplest way to start is by fetching all products:

javascript
// Using fetch API
const response = await fetch('https://api.example.com/api/products');
const data = await response.json();

if (response.ok) {
  console.log('Products:', data.products);
  console.log('Total items:', data.pagination.total);
} else {
  console.error('Error:', data.message);
}


#### Handling Responses

All successful responses follow a consistent structure. Product listings include both the data and pagination information:

javascript
{
  "products": [...],  // Array of product objects
  "pagination": {     // Pagination metadata
    "total": 42,
    "page": 1,
    "limit": 20,
    "pages": 3
  }
}


#### Implementing Filtering

The API supports several filtering options. Here's how to implement common use cases:

**Category Filtering:**
javascript
const getElectronics = async () => {
  const response = await fetch('/api/products?category=electronics');
  return await response.json();
};


**Price Range Filtering:**
javascript
const getProductsInRange = async (min, max) => {
  const params = new URLSearchParams({
    minPrice: min.toString(),
    maxPrice: max.toString()
  });
  
  const response = await fetch(/api/products?${params});
  return await response.json();
};


**Stock Availability:**
javascript
const getAvailableProducts = async () => {
  const response = await fetch('/api/products?inStock=true');
  return await response.json();
};


#### Implementing Pagination

For large product catalogs, implement pagination to improve performance:

javascript
const getProductsPage = async (pageNumber = 1, itemsPerPage = 20) => {
  const params = new URLSearchParams({
    page: pageNumber.toString(),
    limit: itemsPerPage.toString()
  });
  
  const response = await fetch(/api/products?${params});
  const data = await response.json();
  
  return {
    products: data.products,
    hasNextPage: pageNumber < data.pagination.pages,
    currentPage: pageNumber,
    totalPages: data.pagination.pages
  };
};


#### Error Handling Best Practices

Always implement proper error handling for API requests:

javascript
const fetchProductSafely = async (productId) => {
  try {
    const response = await fetch(/api/products/${productId});
    
    if (!response.ok) {
      if (response.status === 404) {
        throw new Error('Product not found');
      } else if (response.status === 400) {
        throw new Error('Invalid product ID format');
      } else if (response.status === 500) {
        throw new Error('Server error - please try again later');
      } else {
        throw new Error(Unexpected error: ${response.status});
      }
    }
    
    return await response.json();
  } catch (error) {
    console.error('Failed to fetch product:', error.message);
    throw error;
  }
};


#### Common Error Scenarios

**Invalid Product ID Format (400):**
This occurs when the product ID doesn't match MongoDB ObjectId format. Validate IDs before making requests:

javascript
const isValidObjectId = (id) => /^[0-9a-fA-F]{24}$/.test(id);

if (!isValidObjectId(productId)) {
  console.error('Invalid product ID format');
  return;
}


**Product Not Found (404):**
Handle cases where products may have been deleted or never existed:

javascript
try {
  const product = await fetchProduct(id);
} catch (error) {
  if (error.message === 'Product not found') {
    // Show user-friendly message or redirect
    showNotFoundMessage();
  }
}


#### Performance Optimization Tips

1. **Use appropriate page sizes**: Start with 20 items per page and adjust based on your UI needs
2. **Implement caching**: Cache frequently accessed product data to reduce API calls
3. **Combine filters**: Use multiple filters in a single request rather than making separate calls

javascript
// Good: Single request with multiple filters
const searchProducts = async (filters) => {
  const params = new URLSearchParams();
  
  if (filters.category) params.set('category', filters.category);
  if (filters.minPrice) params.set('minPrice', filters.minPrice);
  if (filters.maxPrice) params.set('maxPrice', filters.maxPrice);
  if (filters.inStock) params.set('inStock', 'true');
  
  const response = await fetch(/api/products?${params});
  return await response.json();
};


#### Complete Integration Example

Here's a complete example showing how to build a product search component:

javascript
class ProductSearch {
  constructor(apiBaseUrl) {
    this.baseUrl = apiBaseUrl;
    this.currentPage = 1;
    this.filters = {};
  }
  
  async search(filters = {}) {
    this.filters = { ...filters };
    this.currentPage = 1;
    return await this.fetchProducts();
  }
  
  async nextPage() {
    this.currentPage += 1;
    return await this.fetchProducts();
  }
  
  async fetchProducts() {
    const params = new URLSearchParams({
      page: this.currentPage.toString(),
      limit: '20'
    });
    
    Object.entries(this.filters).forEach(([key, value]) => {
      if (value !== null && value !== undefined && value !== '') {
        params.set(key, value.toString());
      }
    });
    
    try {
      const response = await fetch(${this.baseUrl}/api/products?${params});
      
      if (!response.ok) {
        throw new Error(API request failed: ${response.status});
      }
      
      return await response.json();
    } catch (error) {
      console.error('Product search failed:', error);
      throw error;
    }
  }
}

// Usage
const productSearch = new ProductSearch('https://api.example.com');

// Search for electronics under $100
const results = await productSearch.search({
  category: 'electronics',
  maxPrice: 100,
  inStock: true
});


This guide provides everything you need to successfully integrate with the Product API. The endpoints are designed to be flexible and performant, supporting the common use cases you'll encounter when building product catalog features.

## Reflection on the Exercise

Working through this documentation exercise taught me several valuable lessons about API documentation:

**Most Challenging Aspects**: The most difficult part was ensuring completeness while maintaining readability. Balancing technical accuracy with developer-friendly explanations required careful consideration of the target audience.

**Prompt Refinement**: I found that being specific about the target audience and desired tone in Prompt 3 significantly improved the output quality. Adding context about common use cases also helped generate more practical examples.

**Format Effectiveness**: The OpenAPI format proved most effective for technical integration, while the Markdown format worked better for developer onboarding and tutorials. The combination provides comprehensive coverage for different use cases.

**Development Workflow Integration**: This approach would fit well into a documentation-driven development process, where API documentation is created alongside or even before implementation, helping to identify edge cases and improve API design before coding begins.

## Deliverables Summary

1. **Original API endpoint code**: JavaScript/Express Product API with GET endpoints for listing and retrieving products
2. **Comprehensive endpoint documentation**: Detailed documentation using Prompt 1 with parameters, responses, examples, and error handling
3. **Converted documentation format**: OpenAPI 3.0 specification for better tooling integration and standardization
4. **Developer usage guide**: Practical guide with code examples, error handling patterns, and integration strategies for intermediate developers

The exercise demonstrated how structured prompting can create comprehensive, multi-format API documentation that serves different developer needs and use cases effectively.