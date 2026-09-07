# Algoritm-Chempionati-Yakuniy-Loyiha-Capstone-
// ==========================================
// "ALGORITM CHEMPIONATI" — YAKUNIY CAPSTONE TIZIMI
// Barcha 8 ta ma'lumotlar tuzilmasi va algoritmlar integratsiyasi
// ==========================================

// --- 1. LINKED LIST: O'yinchilar ro'yxatini boshqarish (CRUD) ---
// Big O: O'qish O(n), Qo'shish (oxiriga) O(1) yoki O(n), O'chirish O(n)
class ListNode {
    constructor(username, rating) {
        this.username = username;
        this.rating = rating;
        this.next = null;
    }
}

class PlayerLinkedList {
    constructor() {
        this.head = null;
        this.size = 0;
    }

    add(username, rating) {
        const newNode = new ListNode(username, rating);
        if (!this.head) {
            this.head = newNode;
        } else {
            let current = this.head;
            while (current.next) current = current.next;
            current.next = newNode;
        }
        this.size++;
    }

    remove(username) {
        if (!this.head) return false;
        if (this.head.username === username) {
            this.head = this.head.next;
            this.size--;
            return true;
        }
        let current = this.head;
        while (current.next) {
            if (current.next.username === username) {
                current.next = current.next.next;
                this.size--;
                return true;
            }
            current = current.next;
        }
        return false;
    }

    toArray() {
        let arr = [];
        let current = this.head;
        while (current) {
            arr.push({ username: current.username, rating: current.rating });
            current = current.next;
        }
        return arr;
    }
}


// --- 2. STACK: O'yinchining Harakatlar Tarixi (Undo/Redo) ---
// Big O: Push O(1), Pop O(1)
class ActionStack {
    constructor() {
        this.stack = [];
    }

    pushAction(action) {
        this.stack.push(action);
    }

    undo() {
        return this.stack.pop();
    }

    peek() {
        return this.stack[this.stack.length - 1];
    }
}


// --- 3. QUEUE: Turnir Matchmaking Navbati (FIFO) ---
// Big O: Enqueue O(1), Dequeue O(1)
class MatchmakingQueue {
    constructor() {
        this.items = [];
    }

    enqueue(player) {
        this.items.push(player);
    }

    dequeue() {
        return this.items.shift();
    }

    isEmpty() {
        this.items.length === 0;
    }

    size() {
        return this.items.length;
    }
}


// --- 4. MERGE SORT: O'yinchilarni Reyting Bo'yicha Tartiblash ---
// Big O: O(n log n) doimiy vaqt
function merge(left, right) {
    let result = [];
    let i = 0, j = 0;
    while (i < left.length && j < right.length) {
        if (left[i].rating >= right[j].rating) { // O'suvchi yoki kamayuvchi
            result.push(left[i]);
            i++;
        } else {
            result.push(right[j]);
            j++;
        }
    }
    return result.concat(left.slice(i)).concat(right.slice(j));
}

function mergeSortPlayers(arr) {
    if (arr.length <= 1) return arr;
    const mid = Math.floor(arr.length / 2);
    const left = mergeSortPlayers(arr.slice(0, mid));
    const right = mergeSortPlayers(arr.slice(mid));
    return merge(left, right);
}


// --- 5. BINARY SEARCH: Saralangan Massivdan Reyting Bo'yicha Qidirish ---
// Big O: O(log n)
function binarySearchPlayer(arr, targetRating) {
    let left = 0;
    let right = arr.length - 1;
    
    while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        if (arr[mid].rating === targetRating) {
            return arr[mid];
        } else if (arr[mid].rating < targetRating) {
            // Agar reyting bo'yicha kamayuvchi tartiblangan bo'lsa
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return null;
}


// --- 6. HASHMAP: O'yinchi Ma'lumotlari Xazinasi (Username -> Stats) ---
// Big O: O(1) o'rtacha holatda
class PlayerHashMap {
    constructor(size = 53) {
        this.buckets = new Array(size);
    }

    _hash(key) {
        let total = 0;
        for (let char of key) total += char.charCodeAt(0);
        return total % this.buckets.length;
    }

    set(username, stats) {
        let index = this._hash(username);
        if (!this.buckets[index]) this.buckets[index] = [];
        
        let bucket = this.buckets[index];
        for (let pair of bucket) {
            if (pair[0] === username) {
                pair[1] = stats;
                return;
            }
        }
        bucket.push([username, stats]);
    }

    get(username) {
        let index = this._hash(username);
        let bucket = this.buckets[index];
        if (bucket) {
            for (let pair of bucket) {
                if (pair[0] === username) return pair[1];
            }
        }
        return null;
    }
}


// --- 7. BST (Binary Search Tree): Reyting Bo'yicha Dinamik Qidirish & Range Search ---
// Big O: Qidirish va qo'shish O(h), muvozanatsizda O(n)
class BSTNode {
    constructor(rating, username) {
        this.rating = rating;
        this.username = username;
        this.left = null;
        this.right = null;
    }
}

class PlayerBST {
    constructor() {
        this.root = null;
    }

    insert(rating, username) {
        this.root = this._insert(this.root, rating, username);
    }

    _insert(node, rating, username) {
        if (!node) return new BSTNode(rating, username);
        if (rating < node.rating) node.left = this._insert(node.left, rating, username);
        else node.right = this._insert(node.right, rating, username);
        return node;
    }

    rangeSearch(minRating, maxRating) {
        let result = [];
        this._rangeSearch(this.root, minRating, maxRating, result);
        return result;
    }

    _rangeSearch(node, min, max, result) {
        if (!node) return;
        if (node.rating > min) this._rangeSearch(node.left, min, max, result);
        if (node.rating >= min && node.rating <= max) result.push({ username: node.username, rating: node.rating });
        if (node.rating < max) this._rangeSearch(node.right, min, max, result);
    }
}


// --- 8. GRAF: Turnir Jadvali (Kim kim bilan o'ynadi) ---
// Big O: O'qish/qo'shish O(1)
class TournamentGraph {
    constructor() {
        this.adjacencyList = new Map();
    }

    addPlayer(username) {
        if (!this.adjacencyList.has(username)) {
            this.adjacencyList.set(username, []);
        }
    }

    addMatch(p1, p2, resultDetails) {
        this.addPlayer(p1);
        this.addPlayer(p2);
        this.adjacencyList.get(p1).push({ opponent: p2, details: resultDetails });
        this.adjacencyList.get(p2).push({ opponent: p1, details: resultDetails });
    }

    getMatches(username) {
        return this.adjacencyList.get(username) || [];
    }
}


// ==========================================
// TIZIMNI ISGA TUSHIRISH VA TEST QILISH (10+ o'yinchi)
// ==========================================

console.log("=== ALGORITM CHEMPIONATI BOSHLANDI ===");

// 1. 10 ta o'yinchini LinkedList va HashMap ga kiritish
const playersList = [
    { username: "CoderPro", rating: 1550 },
    { username: "JS_Ninja", rating: 1420 },
    { username: "AlgoMaster", rating: 1680 },
    { username: "BugHunter", rating: 1300 },
    { username: "DataWizard", rating: 1600 },
    { username: "ByteCoder", rating: 1250 },
    { username: "CodeQueen", rating: 1510 },
    { username: "RecursiveBoy", rating: 1380 },
    { username: "StackOverflow", rating: 1750 },
    { username: "GraphGuru", rating: 1490 }
];

const linkedList = new PlayerLinkedList();
const hashMap = new PlayerHashMap();
const bst = new PlayerBST();
const queue = new MatchmakingQueue();
const graph = new TournamentGraph();

playersList.forEach(p => {
    linkedList.add(p.username, p.rating);
    hashMap.set(p.username, { rating: p.rating, status: "Active" });
    bst.insert(p.rating, p.username);
    queue.enqueue(p);
    graph.addPlayer(p.username);
});

console.log(`\n1. Jami o'yinchilar (LinkedList orqali): ${linkedList.size} ta.`);

// 2. Merge Sort yordamida turnir reytingini tuzish
let allPlayersArray = linkedList.toArray();
let sortedPlayers = mergeSortPlayers(allPlayersArray);
console.log("\n2. Merge Sort natijasi (Reyting bo'yicha eng kuchlilar):");
console.table(sortedPlayers);

// 3. Binary Search orqali ma'lum reytingli o'yinchini topish
console.log("\n3. Binary Search qidiruvi (Reytingi 1600 bo'lgan o'yinchi):");
console.log(binarySearchPlayer(sortedPlayers, 1600));

// 4. Stack yordamida o'yinchi harakati (Undo/Redo)
const actionStack = new ActionStack();
actionStack.pushAction("Qadam 1: Kod yozildi");
actionStack.pushAction("Qadam 2: Testdan o'tkazildi");
console.log("\n4. Stack Undo amali:", actionStack.undo());

// 5. BST orqali Range Search (Reytingi 1400 dan 1600 gacha bo'lganlar)
console.log("\n5. BST Range Search (Reyting [1400 - 1600]):");
console.table(bst.rangeSearch(1400, 1600));

// 6. Queue va Graf orqali match-making va o'yinlar o'tkazish
console.log("\n6. Turnir O'yinlari (Matchmaking Queue va Graph):");
while (queue.size() >= 2) {
    let p1 = queue.dequeue();
    let p2 = queue.dequeue();
    graph.addMatch(p1.username, p2.username, "G'olib aniqlandi");
    console.log(`O'yin o'tkazildi: ${p1.username} vs ${p2.username}`);
}

console.log("\nCoderPro o'yinlari tarixi (Graf orqali):");
console.log(graph.getMatches("CoderPro"));

console.log("\n=== CHEMPIONAT MUVAFFAQIYATLI YAKUNLANDI ===");
