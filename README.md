📌 環境構築
1. Docker 起動

プロジェクト直下で以下を実行してください。

docker-compose up -d --build


※ 初回は php artisan migrate --seed を実行し、必要なテーブルとダミーデータを投入してください。

📧 メール通知（任意機能）

本プロジェクトでは、勤怠修正申請の通知メール送信を想定しています。
テスト環境では Mailtrap
 を利用できます。

Mailtrap に登録

.env の MAIL_MAILER ～ MAIL_ENCRYPTION を差し替え

MAIL_FROM_ADDRESS を任意で設定

📊 テーブル仕様
users（ユーザー / 管理者）
カラム名	型	PK	UK	NN	FK
id	bigint	◯		◯	
name	varchar(255)			◯	
email	varchar(255)		◯	◯	
password	varchar(255)			◯	
role	enum(employee, admin)			◯	
created_at	timestamp				
updated_at	timestamp				
attendances（勤怠記録）
カラム名	型	PK	NN	備考
id	bigint	◯	◯	
user_id	bigint		◯	users(id)
work_date	date		◯	勤務日
clock_in_at	timestamp			出勤時刻
clock_out_at	timestamp			退勤時刻
status	enum(off, working, on_break, clocked_out)		◯	勤務状態
note	text			備考
created_at	timestamp			
updated_at	timestamp			
break_times（休憩記録）
カラム名	型	PK	NN	備考
id	bigint	◯	◯	
attendance_id	bigint		◯	attendances(id)
break_start	timestamp			開始時刻
break_end	timestamp			終了時刻
created_at	timestamp			
updated_at	timestamp			
attendance_correction_requests（勤怠修正申請）
カラム名	型	PK	NN	備考
id	bigint	◯	◯	
attendance_id	bigint		◯	attendances(id)
user_id	bigint		◯	users(id)
requested_clock_in_at	timestamp			修正後出勤時刻
requested_clock_out_at	timestamp			修正後退勤時刻
requested_break_start	timestamp			修正後休憩開始
requested_break_end	timestamp			修正後休憩終了
note	text		◯	理由
status	enum(pending, approved, rejected)		◯	承認状態
admin_comment	text			却下理由
approved_at	timestamp			承認日時
created_at	timestamp			
updated_at	timestamp			
📐 ER図



👤 テストアカウント

一般ユーザ（従業員）:

email: employee1@example.com / password: password

email: employee2@example.com / password: password

管理者:

email: admin@example.com / password: adminpass

🧪 テストについて
PHPUnit 実行手順
# テスト用DB作成
docker-compose exec mysql bash
mysql -u root -p
# パスワード: root
create database test_database;

# マイグレーション & テスト実行
docker-compose exec php bash
php artisan migrate:fresh --env=testing
./vendor/bin/phpunit

🚀 起動方法
docker-compose up -d


ブラウザで以下にアクセスできます：

一般ユーザー: http://localhost:81/login

管理者ログイン: http://localhost:81/admin/login

phpMyAdmin: http://localhost:8080
