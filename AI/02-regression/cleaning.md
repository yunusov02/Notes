```python
df.columns = df.columns.str.replace(' ', '_').str.lower()

df.columns
```


```python
# df.columns[df.dtypes == 'str']

for column in df.columns[df.dtypes == 'str']:
	df[column] = df[column].str.lower().str.replace(' ', '_')
```


![[Screenshot From 2026-07-13 09-11-15.png]]

**Type of Distributions**

1. Normal Distribution
	- symmetric
	- mean = median = mode
	- few extreme values
2. Left OR Right Skewed Distribution
![[right-left-distribution.jpeg]]


### Long tail Distribution

we need to get rid off long tail values
to do this we use
```python
np.log1p(df[column])
```

