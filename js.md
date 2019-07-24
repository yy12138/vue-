1、函数声明提前会比变量声明提前更前，变量声明提前只提前变量，赋值停留在原处。

2、判断子类是否属于该父类  instanceof

3 let obj = {}; //优雅数组对象去重
						useData = useData.reduce((cur, next) => {
							obj[next.agents_id] ? "" : obj[next.agents_id] = true && cur.push(next);
							return cur;
						}, []);
