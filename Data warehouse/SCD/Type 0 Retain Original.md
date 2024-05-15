First type of SCD in which we only retain the original data. This is applicable if there are no changes in our dimensions. So we really need to make sure that there are no changes occurring in our dimension. And in that case, of course, we don't need to do anything. We don't need to implement any strategy. We can just leave the dimensions as they are. This is usually applicable for date tables except there can be some attributes in this date table, for example, company holidays that might be subject to changes as well. But in general, the date table is really a static table that is not really having any changes.

Properties of type 0:
- There won't be any changes
- Date Table (expect for holidays etc.)
- Original
- Very simple and easy to maintain