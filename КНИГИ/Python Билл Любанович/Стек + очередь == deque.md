
Реализует в себе и стек и очередь с возможностью удаления с начала и с конца списка
```
def palindrome(word):
	from collections import deque
	dq = deque(word)
	while len(dq) > 1:
		if dq.popleft() != dq.pop():
			return False
	return True
```
