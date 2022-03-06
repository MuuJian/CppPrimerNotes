# Chapter12

shared_prt<T> sp = make_shared<T>(args)
shared_prt<T> sp(p)
sp.reset(p, d)
sp.unique()
p.use_count()

unique_ptr<T> u 不支持赋值和拷贝 但是可以拷贝一个右值
u.release()
u.reset(p)

weak_ptr<T> wp(sp)
wp = sp
w.reset()
w.use_count()
w.lock()
w.expired()
