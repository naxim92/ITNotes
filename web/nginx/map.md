#nginx
- Внтури мапов могут быть регэкспы, например:
```
map $status $loggable {
	~^[3]    1;
	deafult  0;
}
```
Здесь внутри стандартной переменной **$status** (См [[web/nginx/variables]]).

