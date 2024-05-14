# Properties of date dimension
- One of the most common & most important dimensions
- Contains date related features
	Year, Month (name & number), Day, Quarter, Week, Weekday (name & number), etc.
- Meaningful surrogate key YYYYMMDD
	For example
	2022-04-02 <-> 20220402
- Extra row for no date/null (source) -> 1900-01-01 (dim)

Time is usually a separate dimension in date dimension. Can be populated in advance (e.g. for next 5 or 10 years).
# Date Features
- Numbers & Text (e.g. January, 1)
- Long & Abbreviated (Jan, January Mon, Monday)
- Combinations of attributes (Q1, 2022 Q1)
- Fiscal dates (Fiscal Year etc.)
- Flags (Weekend, company holidays etc.)

![[Dimension Date dimension.png]]