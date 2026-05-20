# Tuần 1: Tổng Quan C++ & Big-O — Bài tập

## 🎯 Mục tiêu tuần này
Hiểu Big-O, phân tích độ phức tạp, ôn tập C++ cơ bản.

---

### Bài 1: Phân tích Big-O ⭐
Xác định Big-O của 10 đoạn code C++ cho trước. Giải thích tại sao.
void funcO1(const vector<int>& arr) {
    if (!arr.empty()) {
        volatile int temp = arr[0]; // Dùng volatile để compiler không tối ưu hóa bỏ qua dòng này
    }
}
### Bài 2: Đo thời gian thực tế ⭐⭐
Dùng `chrono` đo thời gian chạy của O(n), O(n²), O(log n) với n = 1.000 → 100.000. In bảng kết quả.
void funcOLogN(const vector<int>& arr) {
    int left = 0;
    int right = arr.size() - 1;
    volatile int target = -1; // Cố tình tìm số không tồn tại để ép vòng lặp chạy đến trường hợp xấu nhất (Worst-case)
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) break;
        if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
### Bài 3: Tối ưu hàm ⭐⭐
Cho 3 hàm O(n²) — tối ưu xuống O(n) hoặc O(n log n). Chứng minh bằng cách đo thời gian.
void funcON(const vector<int>& arr) {
    volatile long long sum = 0;
    for (int num : arr) {
        sum += num;
    }
}
### Bài 4: 🔥 Dự Án Mini — Big-O Benchmark Tool ⭐⭐⭐
> **Cảm hứng:** [algorithm-visualizer.org](https://algorithm-visualizer.org)
void funcON2(const vector<int>& arr) {
    volatile long long sum = 0;
    int n = arr.size();
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            sum += 1;
        }
    }
}
Viết chương trình **BenchmarkTool** hiển thị bảng so sánh tốc độ các thuật toán:
```
╔══════════════╦══════════╦══════════╦══════════╗
║   Thuật toán ║  n=1000  ║  n=10000 ║ n=100000 ║
╠══════════════╬══════════╬══════════╬══════════╣
║    O(1)      ║  0.001ms ║  0.001ms ║  0.001ms ║
║    O(log n)  ║  0.003ms ║  0.004ms ║  0.005ms ║
║    O(n)      ║  0.12ms  ║  1.2ms   ║  12ms    ║
║    O(n²)     ║  8ms     ║  800ms   ║  80000ms ║
╚══════════════╩══════════╩══════════╩══════════╝
```
void printLine(ostream& os) {
    os << "||--------------||-------------||-------------||-------------||" << endl;
}

int main() {
    // Các mức n cần đo lường
    vector<int> sizes = {1000, 10000, 100000};
    vector<string> algorithms = {"O(1)", "O(log n)", "O(n)", "O(n^2)"};
    
    // Mảng chứa các con trỏ hàm để dễ dàng lặp
    void (*funcs[])(const vector<int>&) = {funcO1, funcOLogN, funcON, funcON2};
    
    // Ma trận lưu kết quả thời gian (4 thuật toán x 3 kích thước n)
    double results[4][3];

    cout << "Đang tiến hành benchmark... (có thể mất một lúc ở mức n=100000 cho O(n^2))" << endl;

    for (size_t col = 0; col < sizes.size(); ++col) {
        int n = sizes[col];
        // Khởi tạo mảng đã sắp xếp (cần thiết cho Binary Search O(log n))
        vector<int> arr(n);
        for (int i = 0; i < n; ++i) {
            arr[i] = i;
        }

        for (size_t row = 0; row < 4; ++row) {
            // Chạy nhiều lần để lấy trung bình nếu thời gian quá nhỏ (tùy chọn)
            // Ở đây ta đo 1 lần trực tiếp
            results[row][col] = measureTime(funcs[row], arr);
        }
    }

    // --- XUẤT KẾT QUẢ RA CONSOLE & FILE ---
    ofstream outFile("benchmark.txt");
    
    // Lambda function để in ra cả console và file cùng lúc
    auto printOutput = [&](ostream& os) {
        printLine(os);
        os << "|| " << left << setw(12) << "Thuật toán" 
           << "|| " << left << setw(11) << "n=1000" 
           << "|| " << left << setw(11) << "n=10000" 
           << "|| " << left << setw(11) << "n=100000" << "||" << endl;
        printLine(os);

        for (size_t row = 0; row < 4; ++row) {
            os << "|| " << left << setw(12) << algorithms[row];
            for (size_t col = 0; col < 3; ++col) {
                // Format số thập phân
                string timeStr = to_string(results[row][col]);
                timeStr = timeStr.substr(0, timeStr.find('.') + 4) + "ms"; // Lấy 3 chữ số thập phân
                
                os << "|| " << left << setw(11) << timeStr;
            }
            os << "||" << endl;
        }
        printLine(os);
    };
**Yêu cầu:** dùng `std::chrono`, hiển thị bảng căn chỉnh đẹp, xuất ra file `benchmark.txt`.

---
📁 Tham khảo: `Chuong1_TongQuan/Chuong1_TongQuan.cpp`
