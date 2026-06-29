# Bí Kiếp Chính Mạch — Git Workflow & Task Tracking

> *"Chính mạch không bao giờ gãy. Nhánh phụ sinh ra để luyện chiêu, không phải để tồn tại mãi mãi. Phong ấn rõ ràng, linh vụ minh bạch — đó là đạo của người tu lập trình trong tông môn ta."*
>
> — Chưởng Môn, 2026

---

## Mục Lục

1. [Thiên Đạo Chính Mạch — Trunk-Based Development](#1-thiên-đạo-chính-mạch--trunk-based-development)
2. [Phong Ấn Kiếm Phổ — Quy Tắc Commit](#2-phong-ấn-kiếm-phổ--quy-tắc-commit)
3. [Nhánh Phụ Luyện Chiêu — Branch Workflow](#3-nhánh-phụ-luyện-chiêu--branch-workflow)
4. [Thỉnh Cầu Nhập Hội Mạch — Pull Request](#4-thỉnh-cầu-nhập-hội-mạch--pull-request)
5. [Linh Vụ Truy Tung — Task & Kanban Tracking](#5-linh-vụ-truy-tung--task--kanban-tracking)
6. [Chiêu Thức Nhật Dụng — Git Thường Ngày](#6-chiêu-thức-nhật-dụng--git-thường-ngày)

---

## 1. Thiên Đạo Chính Mạch — Trunk-Based Development

> *"Vạn kiếm quy tông — mọi công pháp đều phải hồi quy chính mạch."*

Tông môn ta dùng **Trunk-Based Development** — triết lý phát triển nơi toàn bộ đệ tử cùng làm việc hội tụ về một chính mạch duy nhất. Không có nhánh `develop` trường tồn. Không có nhánh `release` sống lâu. Chỉ có `main` và những nhánh phụ ngắn ngủi tồn tại để luyện từng chiêu.

### Tại Sao Không Dùng Gitflow?

Gitflow sinh ra hai vấn đề cốt lõi:
- Nhánh `develop` tích lũy thay đổi quá lâu → conflict nặng khi merge vào `main`
- Chu kỳ release dài → phát hiện và sửa bug chậm trễ

Trunk-Based Development giải quyết cả hai: nhánh phụ sống tối đa 1–2 ngày, merge liên tục, `main` luôn sẵn sàng xuất trận.

### Sơ Đồ Chi Mạch Tông Môn

```
main (chính mạch — luôn deploy được)
 |
 +-- feat/them-chuc-nang-event     (tồn tại 1-2 ngày)
 |       └── commit, commit → Pull Request → merge → xóa nhánh
 |
 +-- fix/sua-loi-dang-nhap         (tồn tại vài giờ)
 |       └── commit → Pull Request → merge → xóa nhánh
 |
main ──► stable/v1.0   (staging, được promote từ main)
main ──► tag v1.0.1    (production release)
```

### Quy Ước Đặt Tên Nhánh

| Tiền Tố | Dùng Khi Nào |
|---|---|
| `feat/ten-tinh-nang` | Thêm chức năng mới |
| `fix/mo-ta-loi` | Sửa bug |
| `chore/ten-cong-viec` | Config, CI/CD, cập nhật dependency |
| `refactor/phan-can-tai-luyen` | Tái cấu trúc, không thay đổi behavior |
| `test/ten-test` | Thêm hoặc sửa test |
| `docs/ten-tai-lieu` | Cập nhật tài liệu |

Tên nhánh dùng **kebab-case**, **tiếng Anh**, **ngắn gọn**.

```bash
# ✅ Đúng
feat/event-lifecycle-state-machine
fix/login-redirect-loop
chore/update-spring-boot-3-2

# ❌ Sai
feature/them-tinh-nang-quan-ly-su-kien-cho-admin-v2   # quá dài
FEAT/EventManagement                                   # sai case
my-branch                                              # không rõ ràng
```

### ⛔ Đại Kỵ — Tuyệt Đối Không Làm

- **Commit thẳng vào `main`** — chỉ Admin trong trường hợp khẩn cấp, phải thông báo ngay
- **Giữ nhánh phụ quá 3 ngày** mà chưa merge — hãy chia nhỏ issue
- **Merge mà không có Pull Request** — mọi thay đổi đều phải qua cổng kiểm duyệt
- **Push `--force` lên `main`** — không bao giờ, dù bất kỳ lý do gì

---

## 2. Phong Ấn Kiếm Phổ — Quy Tắc Commit

> *"Mỗi phong ấn là một trang kiếm phổ. Viết rõ ràng để hậu thế còn đọc được."*

Tông môn ta tuân thủ **Conventional Commits** — chuẩn phong ấn mà toàn bộ tự động trận pháp (GitHub Actions, changelog, semantic versioning) dựa vào để vận hành.

### Cấu Trúc Phong Ấn

```
<type>(<scope>): <description>

[body — chỉ viết khi "tại sao" không rõ ràng]

[footer — dòng closes #N]
```

| Phần | Bắt Buộc? | Mô Tả |
|---|---|---|
| `type` | ✅ | Loại thay đổi |
| `scope` | Tùy chọn | Phạm vi: `event`, `task`, `auth`... |
| `description` | ✅ | Viết thường, không chấm cuối, ≤50 ký tự |
| `body` | Tùy chọn | Giải thích *tại sao*, không giải thích *cái gì* |
| `footer` | Tùy chọn | `closes #N` để đóng issue liên kết |

### Các Loại Phong Ấn

| Type | Dùng Khi Nào |
|---|---|
| `feat` | Thêm chức năng mới cho người dùng |
| `fix` | Sửa bug ảnh hưởng đến người dùng |
| `chore` | Việc nội bộ: config, dependency, không ảnh hưởng logic |
| `refactor` | Tái cấu trúc, không thay đổi behavior |
| `test` | Thêm hoặc sửa test |
| `docs` | Cập nhật tài liệu |
| `ci` | Thay đổi GitHub Actions workflow |
| `perf` | Cải thiện hiệu năng |

### Phong Ấn Đúng vs Sai

```bash
# ✅ Đúng
feat(event): add 5-step creation wizard
fix(auth): redirect to dashboard after Azure SSO login
chore: upgrade spring-boot to 3.2.4
refactor(task): extract status transition logic to TaskStateMachine
docs: add branch strategy section to README

# ❌ Sai
fix bug                          # Quá chung chung
update code                      # Không nói lên gì
WIP                              # Không phải commit hoàn chỉnh
feat: Add Event Management Feature and also fix some bugs and update README
                                 # Một commit cho nhiều thứ — hãy chia nhỏ
```

### Khi Nào Cần Viết Body?

Body chỉ cần khi lý do thay đổi **không tự hiển nhiên** từ description:

```bash
refactor(task): replace status enum with state machine pattern

The previous if-else chain in TaskService.updateStatus() was becoming
unmaintainable as we added Cancelled and Pending states for Posting Tasks.
The state machine enforces valid transitions at compile time.
```

> 💡 **Pháp Quyết:** Dùng `git add <file>` theo tên cụ thể, không dùng `git add .` — tránh vô tình commit file `.env` hay build artifact.

---

## 3. Nhánh Phụ Luyện Chiêu — Branch Workflow

> *"Ra nhánh để luyện, quy tông để hoàn thành. Nhánh phụ không phải chỗ ở — là chỗ luyện."*

### Vòng Đời Một Chi Mạch

**Bước 1 — Tạo issue trước, branch sau.**

```bash
# Luôn bắt đầu từ main mới nhất
git checkout main
git pull origin main

# Tạo nhánh từ main
git checkout -b feat/event-lifecycle-state-machine
```

**Bước 2 — Commit thường xuyên, nhỏ, có nghĩa.**

```bash
git status
git add src/main/java/com/cgsem/event/EventStateMachine.java
git commit -m "feat(event): implement Draft->Active state transition"
```

**Bước 3 — Push và tạo Pull Request.**

```bash
git push origin feat/event-lifecycle-state-machine

# Tạo PR bằng GitHub CLI
gh pr create --base main \
  --title "feat: event lifecycle state machine" \
  --body "closes #5"
```

**Bước 4 — Dọn dẹp sau khi merge.**

```bash
git checkout main
git pull origin main
git branch -d feat/event-lifecycle-state-machine
git push origin --delete feat/event-lifecycle-state-machine
```

### Đồng Bộ Với Main Khi Bị Tụt Hậu

```bash
git fetch origin
git rebase origin/main

# Nếu có conflict:
# 1. Sửa file conflict
# 2. git add <file-đã-sửa>
# 3. git rebase --continue
```

> 💡 **Pháp Quyết:** Dùng `rebase` để đồng bộ với main trong nhánh phụ — không dùng `merge main` vào nhánh phụ, nó tạo merge commit thừa làm lịch sử rối loạn.

---

## 4. Thỉnh Cầu Nhập Hội Mạch — Pull Request

> *"Không ai tự phong mình đã hoàn thành — phải qua cổng kiểm duyệt của đồng môn."*

### PR Template — Bắt Buộc Điền Đầy Đủ

```markdown
## Summary
Thêm state machine cho Event lifecycle, thay thế if-else trong EventService.

## Linked Issues
closes #5

## Type of Change
- [x] Feature

## Checklist
- [x] Tests added / updated
- [x] No breaking changes
```

### ⛔ Thiếu `closes #N` = Kanban Không Tự Động

Nếu PR không có dòng `closes #N`, GitHub Actions không thể di chuyển issue trên bảng Kanban. Issue sẽ mắc kẹt ở **Todo** thay vì chuyển sang **In Review**. **Mọi PR bắt buộc phải link issue.**

### Điều Gì Xảy Ra Sau Khi Tạo PR?

| Sự Kiện | Trận Pháp Tự Động Làm |
|---|---|
| PR được tạo | Issue → **In Review** trên Kanban |
| PR được merge | Issue → **Done**, tự động đóng |
| Tag `vX.X.X` push | GitHub Release tạo, issue nhận nhãn `released:vX.X.X` |
| Issue được assign | Issue → **In Progress** |

### Quy Trình Code Review

1. Tạo PR, assign reviewer
2. Reviewer để lại comment nếu cần chỉnh
3. Sửa trực tiếp trên nhánh, commit thêm, push — PR tự cập nhật
4. Sau khi approved: người tạo PR tự merge
5. Xóa nhánh sau khi merge

> 💡 **Squash and merge** khi nhánh có nhiều commit WIP vụn vặt. **Create a merge commit** khi muốn giữ lịch sử chi tiết. Tông môn ta ưu tiên **Squash and merge** cho feature branches.

---

## 5. Linh Vụ Truy Tung — Task & Kanban Tracking

> *"Linh vụ không có trong kiếm phổ là linh vụ không tồn tại."*

**Quy tắc vàng:** Không bao giờ bắt đầu viết code mà chưa có issue.

### Chọn Template Đúng

| Template | Dùng Khi Nào |
|---|---|
| **Feature** | Một chức năng cụ thể, hoàn thành trong 1–2 ngày |
| **Epic** | Nhóm chức năng lớn, chứa nhiều Feature issue con |
| **Bug** | Lỗi cần sửa |

### Điền Issue Đúng Cách

```
Feature title: Implement Event status transition API
Module: F1 - Event Management
User Story reference: US-A2
Priority: 🔴 High
Effort estimate: M — 3-5 days
Start Date: 2026-07-01
End Date (Deadline): 2026-07-05

Acceptance Criteria:
- [ ] PATCH /api/events/{id}/status endpoint
- [ ] Validate allowed transitions
- [ ] Unit tests for all transition paths
```

> 💡 **Start Date + End Date** là bắt buộc — chúng là nhiên liệu cho **Roadmap View (Gantt chart)** trên bảng Kanban. Thiếu hai trường này, issue trở thành điểm mù trên roadmap.

### Vòng Đời Một Issue

```
Tạo issue      ──► Kanban: TODO        (tự động)
Issue assigned ──► Kanban: IN PROGRESS (tự động)
Mở PR "closes #N" ──► Kanban: IN REVIEW  (tự động)
PR merged      ──► Kanban: DONE        (tự động)
Tag vX.X.X     ──► nhãn "released:vX.X.X" (tự động)
```

### Kanban Board

🔗 **https://github.com/orgs/cgsem-superapp/projects/1**

| Cột | Màu | Ý Nghĩa |
|---|---|---|
| Todo | Xám | Issue đã tạo, chưa ai bắt đầu |
| In Progress | Xanh | Đang có người làm |
| In Review | Vàng | PR đang mở, chờ review |
| Done | Xanh lá | PR đã merge hoặc issue đã đóng |

Ngoài tab **Board**, dùng tab **Roadmap** để xem Gantt chart theo Start Date / End Date.

---

## 6. Chiêu Thức Nhật Dụng — Git Thường Ngày

> *"Chiêu thức luyện nghìn lần thành phản xạ."*

### Bắt Đầu Ngày Mới

```bash
git checkout main
git pull origin main

# Tiếp tục nhánh đang làm
git checkout feat/ten-tinh-nang-cua-ban
git rebase origin/main
```

### Trong Quá Trình Làm Việc

```bash
git status                          # luôn chạy trước mọi thao tác
git diff                            # xem thay đổi trước khi add
git diff --staged                   # xem những gì đã staged
git log --oneline -10               # xem lịch sử gần nhất
git blame <file>                    # tìm ai viết dòng nào
```

### Xử Lý Conflict

```bash
# Sau khi rebase báo conflict:
git status                          # xem file nào bị conflict

# Mở file, tìm dấu <<<<<<, sửa tay, xóa marker
# Sau khi sửa:
git add <file-đã-sửa>
git rebase --continue

# Muốn hủy toàn bộ rebase:
git rebase --abort
```

> ⚠️ Nếu conflict xuất hiện ở nhiều file và phức tạp — **đừng tự giải quyết một mình**. Gọi người đã viết code đó (dùng `git blame`) và giải quyết cùng nhau.

### Tạm Phong Ấn Với Stash

```bash
# Lưu thay đổi dang dở khi cần chuyển việc gấp
git stash push -m "dang lam event lifecycle, chua xong"

git stash list                      # xem danh sách stash
git stash pop                       # lấy stash mới nhất ra
```

### Cấp Cứu

```bash
# Undo commit chưa push (giữ lại thay đổi trong file)
git reset --soft HEAD~1

# Undo commit đã push lên nhánh phụ (an toàn hơn reset)
git revert <commit-hash>
git push origin feat/ten-nhanh

# Tìm lại commit đã mất
git reflog                          # Git lưu mọi hành động 90 ngày
git cherry-pick <commit-hash>       # lấy commit đó vào nhánh hiện tại
```

> ⛔ **Không dùng `--force-push`** lên `main`, `stable/*`, hoặc nhánh người khác đang dùng. Hành động này xóa commit của đồng môn.

---

## Checklist Đệ Tử

- [ ] Hiểu trunk-based development và tại sao tông môn dùng nó
- [ ] Biết đặt tên nhánh theo đúng tiền tố (`feat/`, `fix/`, `chore/`...)
- [ ] Viết commit message theo Conventional Commits
- [ ] Luôn tạo issue trước khi tạo branch
- [ ] Điền đầy đủ Start Date, End Date, Priority, Module trong issue template
- [ ] PR bắt buộc có dòng `closes #N`
- [ ] Biết resolve conflict bằng rebase
- [ ] Biết dùng stash khi cần chuyển việc gấp
- [ ] Biết dùng `git reflog` để phục hồi khi lỡ tay
- [ ] Không bao giờ commit trực tiếp vào `main`

---

> *"Đệ tử nắm vững bí kiếp này không phải để làm hài lòng Chưởng Môn, mà để tự bảo vệ mình và bảo vệ đồng môn khỏi những sai lầm không đáng có."*
>
> ✦ Chính Mạch Bất Diệt — Trường Tồn Cùng Tông Môn ✦
