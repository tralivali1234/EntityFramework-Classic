
# ThenInclude

## Description
You can chain multiple related objects to the query result by using the `AlsoInclude` and `ThenInclude` methods. The `ThenInclude` method moves the chaining level to the property included. It allows to include related objects from the next level.

`ThenInclude` is a syntactic sugar method to make it easier and clearer to include multiple related objects.

### Note
- If you want to reset the level to the root, use [`Include`](include.md)
- If you want to include items from the same level, use [`AlsoInclude`](also-include.md)

## Examples
```csharp
ctx.Customers
	.Include(customer => customer.Orders)
		.ThenInclude(order => order.OrderDetails)
		.ThenInclude(orderDetail => orderDetail.Product)
	.ToList();
```

[Try it](https://dotnetfiddle.net/o3xWDl)

```csharp
ctx.Customers
	.Include(customer => customer.Orders)
		.ThenInclude(order => order.OrderDetails.Select(orderDetail => orderDetail.Product))
	.ToList();
```

[Try it](https://dotnetfiddle.net/0VC0W1)

```csharp
ctx.OrderDetails
	.Include(orderDetail => orderDetail.Product)
		.ThenInclude(product => product.Category)
	.Include(orderDetail => orderDetail.Product)
		.ThenInclude(product => product.Supplier)
	.ToList();
```

[Try it](https://dotnetfiddle.net/DzQzed)

## Limitations

### DbQuery
Chaining includes only work if the first include call is from a `DbQuery`. If you used some LINQ and the query is currently an `IQueryable`, you can use the method `AsDbQuery` to tell the compiler that's a `DbQuery`.
This restriction is currently required to avoid some side impact with queries that are not directly using `DbQuery` class.

```csharp
ctx.OrderDetails
	.Where(orderDetail => orderDetail.Quantity > 1)
	.AsDbQuery()
	.Include(orderDetail => orderDetail.Product)
		.ThenInclude(product => product.Category)
	.Include(orderDetail => orderDetail.Product)
		.ThenInclude(product => product.Supplier)
	.ToList();
```

[Try it](https://dotnetfiddle.net/CFPbYm)
