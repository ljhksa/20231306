# 3번 코드
import csv
from datetime import datetime

class Heap:
    def __init__(self,*args):
        if len(args) != 0:
            self.__A = args[0]
        else:
            self.__A = []

    def insert(self, x):
        self.__A.append(x)
        self.__percolateUp(len(self.__A) - 1)

    def __percolateUp(self, i: int):
        parent = (i - 1) // 2
        if i > 0 and self.__A[i] > self.__A[parent]:
            self.__A[i], self.__A[parent] = self.__A[parent], self.__A[i]
            self.__percolateUp(parent)

    def deleteMax(self):
        if not self.isEmpty():
            max = self.__A[0]
            self.__A[0] = self.__A.pop()
            self.__percolateDown(0)
            return max
        else:
            return None

    def __percolateDown(self, i: int):
        child = 2 * i + 1
        right = 2 * i + 2
        if child <= len(self.__A) - 1:
            if right <= len(self.__A) - 1 and self.__A[child] < self.__A[right]:
                child = right
            if self.__A[i] < self.__A[child]:
                self.__A[i], self.__A[child] = self.__A[child], self.__A[i]
                self.__percolateDown(child)

    def isEmpty(self) -> bool:
        return len(self.__A) == 0

heap = Heap()

with open('birthday.csv', newline='', encoding='cp949') as csvfile:
    reader = csv.reader(csvfile)
    next(reader)

    for row in reader:
        try:
            name = row[1].strip()
            birth_str = row[2].strip()

            if birth_str == "":
                continue

            birth_date = datetime.strptime(birth_str, "%Y-%m-%d")
            heap.insert((birth_date, name))

        except Exception as e:
            print(f"건너뜀: {row} -> 에러: {e}")
            continue

print("생일이 늦은 순으로 10명:")
count = 0
while not heap.isEmpty() and count < 10:
    birth_date, name = heap.deleteMax()
    print(f"{name}: {birth_date.strftime('%Y-%m-%d')}")
    count += 1
# 4번코드
from datetime import datetime
import csv

class BidirectNode:
    def __init__(self, item, prev=None, next=None):
        self.item = item
        self.prev = prev
        self.next = next


class CircularDoublyLinkedList:
    def __init__(self):
        self.__head = BidirectNode("dummy")
        self.__head.prev = self.__head
        self.__head.next = self.__head
        self.__numItems = 0

    def append(self, newItem):
        prev = self.__head.prev
        newNode = BidirectNode(newItem, prev, self.__head)
        prev.next = newNode
        self.__head.prev = newNode
        self.__numItems += 1

    def __iter__(self):
        return CircularDoublyLinkedListIterator(self)

class CircularDoublyLinkedListIterator:
    def __init__(self, alist):
        self.head = alist._CircularDoublyLinkedList__head
        self.iterPosition = self.head.next

    def __next__(self):
        if self.iterPosition == self.head:
            raise StopIteration
        else:
            item = self.iterPosition.item
            self.iterPosition = self.iterPosition.next
            return item

    def __iter__(self):
        return self

team_members = {
    "오예준", "김서빈", "김선민", "김예빈", "김주하", "김채민",
    "김혜인", "김민주", "배시은", "송민서", "안수민", "최가온", "신희영"
}

birth_list = CircularDoublyLinkedList()

with open('birthday.csv', newline='', encoding='cp949') as csvfile:
    reader = csv.reader(csvfile)
    next(reader)

    for row in reader:
        if len(row) >= 3:
            name = row[1].strip()
            birth_str = row[2].strip()

            try:
                birth_date = datetime.strptime(birth_str, "%Y-%m-%d")
                birth_list.append((name, birth_date))
            except ValueError:
                birth_list.append((name, "비어있음"))

print("같은 조 친구들의 생일 정보:")
for name, birth in birth_list:
    if name in team_members:
        if birth == "비어있음":
            print(f"{name}: 비어있음")
        else:
            print(f"{name}: {birth.strftime('%Y-%m-%d')}")
