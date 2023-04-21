---
layout: post
title:  "Using Elixir Stream to download large amount of data from DB"
date:   2023-03-21 11:00:00 +0000
categories: elixir development api database
---

Today, we're going to explore the topic of building efficient marketing capabilities using Elixir's Stream module. As part of the TEMAKI team at Marley Spoon, we understand the importance of optimizing promotional campaigns that use thousands of vouchers, while still delivering a seamless user experience. In this post, I'll take you through the steps of leveraging Elixir's powerful Stream module to reduce memory load and database usage, allowing for smoother voucher creation and export. So, let's dive in and see how we can optimize our marketing capabilities!

# Fetching data from database

To fetch data from a Postgres database using Elixir and Phoenix, you can use the Ecto library, which provides a powerful and flexible way to work with databases in Elixir. Here's an example of how to fetch all records from a `vouchers` table using Ecto:

```elixir
vouchers = MyApp.Repo.all(MyApp.Voucher)
```

This will fetch all records from the `vouchers` table and return them as a list of `MyApp.Voucher` structs. You can then use these structs to manipulate or display the data as needed.

When you have a large amount of data to fetch, the approach of fetching all records from the database at once using `Repo.all` can lead to performance issues and memory consumption problems.

This is because fetching a large number of records from the database can take a long time, and the resulting data set may be too large to fit into memory all at once. This can cause your application to become slow and unresponsive, or even crash if it runs out of memory.

To avoid these issues, you can use pagination or streaming to fetch data in smaller, more manageable chunks. Pagination involves fetching a limited number of records at a time and using cursor-based navigation to fetch subsequent pages. Streaming involves fetching records one at a time or in batches, and processing them as they arrive.

# The Elixir Stream module

The Stream module in Elixir provides a way to work with collections in a lazy and efficient manner. Instead of eagerly loading all the elements of a collection into memory, a stream generates each element on-the-fly and only when it's needed.

Streams are composable, meaning that you can chain multiple stream operations together to create complex transformations on data. This makes streams a powerful tool for working with large data sets, as they allow you to process data in a memory-efficient and scalable way.

Overall, the Stream module is a key feature of Elixir that allows developers to build high-performance, scalable applications that can handle large amounts of data with ease.

```elixir
MyApp.Repo.stream(MyApp.Voucher)
|> Stream.each(fn voucher ->
  # Process each record as it arrives
  IO.puts "voucher: #{voucher.code} (#{voucher.discount})"
end)
|> Stream.run()
```

This code uses `Repo.stream` to create a stream of records from the users table, and then pipes that stream through a series of operations. The `Stream.each` function is used to process each record as it arrives, and the `Stream.run` function is used to execute the stream.

By using streaming, this code can fetch a large number of records from the database without loading them all into memory at once. Instead, records are fetched and processed one at a time as they arrive, allowing the application to handle large data sets in a more efficient and scalable manner.

```elixir
Repo.transaction(fn ->
  MyApp.Repo.stream(MyApp.Voucher)
  |> Stream.chunk_every(100)
  |> Stream.each(fn chunk ->
    # Process each chunk of records as it arrives
    Enum.each(chunk, fn voucher ->
      IO.puts "voucher: #{voucher.code} (#{voucher.discount})"
    end)
  end)
  |> Stream.run()
end)
```

By wrapping the stream operations in a transaction, we're able to safely execute the stream and ensure that all changes to the database are rolled back if any errors occur. This helps to ensure data consistency and prevent data corruption in the event of an error.

In this code, we're also using `Stream.chunk_every(100)` to split the stream into chunks of 100 records each. This means that instead of processing each record individually, we're processing each chunk of records as a group.

Using chunks can be beneficial when working with large data sets because it reduces the overhead of processing each individual record. Instead, we can process batches of records at a time, which can be more efficient and can reduce the overall processing time.

In addition, using chunks can also help to reduce memory consumption by limiting the amount of data that needs to be loaded into memory at once.
