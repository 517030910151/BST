# BST
实现二叉查找树，支持插入，删除，查找，删除小于 x 的所有元素，删除大于 x 的所有元素，删除大于 a 且小于 b 的所有元素，查找第 i 小的元素
#include <iostream>
#include <cstring>
using namespace std;
struct BinaryNode
{
	int data;
	BinaryNode *left;
	BinaryNode *right;

	BinaryNode(const int& theData, BinaryNode *lt = NULL, BinaryNode *rt = NULL)
		:data(theData), left(lt), right(rt) {}
};
BinaryNode *root = NULL;

void find(const int& x, BinaryNode* t) {
	if (t == NULL) { cout << 'N' << '\n'; return; }
	if (t->data == x) { cout << 'Y' << '\n'; return; }
	if (x < t->data) find(x, t->left);
	else find(x, t->right);
}

void insert(const int &x, BinaryNode*& t) {
	if (t == NULL)
		t = new BinaryNode(x, NULL, NULL);
	else if (x <= t->data)
		insert(x, t->left);
	else if (x > t->data)
		insert(x, t->right);
}

void remove(const int &x, BinaryNode*& t) {
	if (t == NULL) { return; }
	if (x < t->data)remove(x, t->left);
	else if (x > t->data)remove(x, t->right);
	else if (t->left != NULL && t->right != NULL) {//if the node has two sons
		BinaryNode* tmp = t->right;
		while (tmp->left != NULL)tmp = tmp->left;
		t->data = tmp->data;
		remove(t->data, t->right);
	}
	else {
		BinaryNode* oldNode = t;//if the node has one or zero son
		t = (t->left == NULL ? t->right : t->left);
		oldNode = NULL; delete oldNode;
	}
}
int Array[1000007];
int currentSize = 0;
void midSort( BinaryNode*& t) {
	if (t == NULL)return;
	midSort(t->left);
	Array[++currentSize] = t->data;
	midSort( t->right);
}
int currentSize2 = 0;
void poSort( BinaryNode*& t) {
	if (t == NULL)return;
	poSort(t->right);
	Array[++currentSize2] = t->data;
	poSort( t->left);
}
void remove_less_than(const int &x, BinaryNode*& t) {//有问题？？？
	/*if (t == NULL) { return; }
	if (x > t->data) {
		t->left = NULL;
		remove(t->data, t);
		remove_less_than(x, t);
	}
	if (x <= t->data) {
		remove_less_than(x, t->left);
	}*/
	/*if (t == NULL) { return; }
	BinaryNode* ll = t->left;
	BinaryNode* rr = t->right;
	//remove_less_than(x, t->left);
	remove_less_than(x, ll);
	int tmp = t->data;
	if (tmp < x)remove(t->data, t);
	else if (tmp >= x) return;
	//remove_less_than(x, t->right);
	remove_less_than(x, rr);*/
	midSort( root);
	for (int i = 1; i <= currentSize; ++i) {
		if (Array[i] < x) {
			remove(Array[i], root);
		}
		else break;
	}
}

void remove_greater_than(const int &x, BinaryNode*& t) {//有问题？？？
	/*if (t == NULL) { return; }
	if (x < t->data) {
		t->right = NULL;
		remove(t->data, t);
		remove_greater_than(x, t);
	}
	if (x >= t->data) {
		remove_greater_than(x, t->right);
	}*/
	/*if (t == NULL) { return; }
	BinaryNode* ll = t->left;
	BinaryNode* rr = t->right;
	//remove_greater_than(x, t->right);
	remove_greater_than(x, rr);
	int tmp = t->data;
	if (tmp > x)remove(t->data, t);
	else if (tmp <= x)return;
	//remove_greater_than(x, t->left);
	remove_greater_than(x, ll);*/
	poSort(root);
	for (int i = 1; i <= currentSize2; ++i) {
		if (Array[i] > x) {
			remove(Array[i], root);
		}
		else break;
	}
}

void remove_interval(const int &x, const int &y, BinaryNode*& t) {
	if (t == NULL) { return; }
	if (t->data <= x) {
		remove_interval(x, y, t->right);
	}
	if (t->data >= y) {
		remove_interval(x, y, t->left);
	}
	else if (t->data > x && t->data < y) {
		remove(t->data, t);
		remove_interval(x, y, t);
	}
}

int count(BinaryNode*& t) {
	if (t == NULL)return 0;
	else return 1 + count(t->left) + count(t->right);
}
int find_ith(const int& x, BinaryNode*& t) {
	if (t == NULL) { cout << 'N' << '\n'; return 0; }
	int num = count(t->left);
	if (num == x - 1)return t->data;
	if (num < x - 1)return find_ith(x - num - 1, t->right);
	return find_ith(x, t->left);
}

char op[50]; int m;
int main() {
	int N; cin >> N;
	for (int i = 1; i <= N; ++i) {
		cin >> op;
		if (!strcmp(op, "insert")) {
			cin >> m;
			insert(m,root);
		}
		if (!strcmp(op, "find")) {
			cin >> m;
			find(m, root);
		}
		if (!strcmp(op, "find_ith")) {
			cin >> m;
			if (find_ith(m, root)) {
				cout << find_ith(m, root) << '\n';
			}
		}
		if (!strcmp(op, "delete")) {
			cin >> m;
			remove(m, root);
		}
		if (!strcmp(op, "delete_less_than")) {//???
			cin >> m;
			remove_less_than(m, root);
			currentSize = 0;
		}
		if (!strcmp(op, "delete_greater_than")) {//???
			cin >> m;
			remove_greater_than(m, root);
			currentSize2 = 0;
		}
		if (!strcmp(op, "delete_interval")) {
			int n; cin >> m >> n;
			remove_interval(m, n, root);
		}
	}
}
