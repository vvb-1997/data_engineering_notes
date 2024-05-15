Sometimes we have a dimension in our data model that is not really a dimension. So this dimension does not have a separate dimension table, but still it functions in a way as a dimension. And that is a so-called degenerate dimension.

Example: The following transactional sales fact table. In this case, we have different transactions and there are different amounts and they can be also summarized together in one payment. So we can have a payment foreign key and a payment dimension. But sometimes all of these relevant attributes have already been extracted by other dimensions, or sometimes also this value of the header is not really important, and therefore we already have all of the relevant information. In that case, we are left only with the primary key of this payment dimension. So of course, in that case, it doesn't really make sense to have this separate dimension because there's no additional information.

![[Dimension Degenerate dimension 1.png]]

Even though we don't have this dimension of the payment, this value of the payment ID can still be of value for our analysis. For example, we want to summarize the amount of each of the payments, or we also want to maybe calculate the average of the payments, so of the amount of the payments, and therefore, we need to be able to group also by this column. So in that case, we would not have this separate dimension table, but we would keep the value of this fact table of, in this case, the payment foreign key.

Yet, of course, in that case, it is not a foreign key anymore, and we should also denote that. So we should explicitly make it clear that this is not a foreign key and that there's no dimension table associated with that. And therefore we could also just add the suffix, in this case, for example, DD.

![[Dimension Degenerate dimension 2.png]]

Properties of Degenerate Dimension
- Degenerate dimension the dimension key without an associated dimension
- Occurring mostly in Transactional facts
- Invoice no., billing no. or order_id typically are degenerate dimensions