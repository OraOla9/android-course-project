# 大项目-UsedCar

[UNIVERSITY NAME REMOVED]

移动终端软件开发

实 验 报 告

实验项目

二手汽车商软件（车易通）

学生姓名

作者

学生学号

2022XXXXXX

学生班级

计算机XX班

完成日期

2025-01-03

实验成绩

评阅日期

评阅教师

目录

一、需求分析3

1.1应用场景3

1.2项目功能3

二、技术选型4

三、项目介绍及设计分析5

3.1数据库设计5

3.1.1车商表（dealer）5

3.1.2车辆表（cars）5

3.2项目总体设计架构7

3.3初始化界面8

3.4登录界面9

3.5注册界面15

3.6用户信息修改或退出界面18

3.7主界面（车商收购车辆展示）22

3.8车辆具体信息26

3.9商家已卖车辆展示页面30

3.10车辆添加32

四、重要代码分析34

4.1从服务器获取Car列表34

4.2SqlLit操作39

4.2.1获取数据库连接39

4.2.2数据库操作42

4.3适配器48

五、项目总结52

一、需求分析

1.1应用场景

开发一个面向二手车商的 Android 应用，记录和管理二手车的收购和销售过程。用户可以在应用中记录每一辆二手车的收购和销售信息，包括价格、时间等。该应用将帮助车商高效跟踪车辆交易历史，并进行统计和分析。

车商收车

当车商收购一辆车时，他们会记录车辆的详细信息，包括品牌、型号、车况、收购价格等。每一辆车都有一个唯一的标识（发动机号）。

收车信息被存储到本地数据库，同时可以选择同步到远程服务器数据库，确保数据安全。

车商卖车

当车商卖掉一辆车时，他们会记录销售信息，包括销售价格、买家信息、销售时间等。

每辆车的销售信息将与收购信息关联，形成完整的交易记录。

车商信息修改：

车商可以修改他的用户名和密码

可以退出当前登录，用其他账号登录

1.2项目功能

1.2.1. 用户管理模块

注册功能：车商通过注册账号创建自己的应用账户。

登录功能：车商输入用户名和密码登录应用。

登出功能：车商可以随时退出登录，清除登录状态。

角色管理：车商可以看到自己收购与卖出去的车，看不到别人的。

1.2.2. 二手车信息管理模块

车辆收购记录：

车商可以记录每辆二手车的收购信息，包括收购时间、收购价格、车主信息、车辆型号、品牌、车牌号、发动机号等。

记录收购车辆的具体情况（如车况、是否有事故历史等）。

车辆销售信息：

车商可以记录每辆二手车的销售信息，包括销售时间、销售价格、买家信息等。

每辆车的销售记录与收购记录关联，以便追踪车的历史。

1.2.3. 数据同步模块

本地缓存：应用使用 SQLite 存储收购与销售信息，确保即使在没有网络的情况下也能正常使用。

数据同步：定期或手动同步本地数据与服务器的数据库，确保数据不会丢失并保持最新。

二、技术选型

前端（Android 客户端）：

编程语言：Java。

UI框架：原生 Android UI 组件。

数据库：SQLite 存储收购与销售信息。

用户信息存储：Shareprefence存储用户ID、用户名、密码。

后端（服务器端）：

技术栈：Java + Spring Boot。

数据库：MySQL 用于存储收购与销售信息，以及用户数据。

数据同步：Android 客户端通过 API 和服务器进行数据同步。

三、项目介绍及设计分析

本次项目主要是Android项目，再此后端的就不过多介绍了

3.1数据库设计

3.1.1车商表（dealer）

用于存储车商的信息，包含注册、登录等信息。

字段说明：

dealer_id：用户的唯一标识。

dealername：用户名，唯一。

password：加密后的密码。

3.1.2车辆表（cars）

用于存储车辆的基本信息，包括收购和销售信息。

字段说明：

car_id：车辆的唯一标识。

engine_number：发动机号，唯一标识每一辆车。

brand：品牌。

model：型号。

grade：车况（S、A、B、C）。

acquisition_price：收购价格。

acquisition_date：收购日期。

sale_price：销售价格（如果该车已售出）。

sale_date：销售日期（如果该车已售出）。

status：状态（available表示可售，sold表示已售）。

dealer_id：外键，指向车商表，表示该车属于哪个车商。

E-R图：

sql

-- 车商CREATE TABLE dealer (    dealer_id INT AUTO_INCREMENT PRIMARY KEY,  -- 车商唯一标识    dealername VARCHAR(255) UNIQUE NOT NULL,   -- 用户名，唯一    password VARCHAR(255) NOT NULL             -- 加密后的密码);-- 车辆CREATE TABLE cars (    car_id INT AUTO_INCREMENT PRIMARY KEY,           -- 车辆唯一标识    engine_number VARCHAR(255) UNIQUE NOT NULL,       -- 发动机号，唯一标识每一辆车    brand VARCHAR(100) NOT NULL,                      -- 品牌    model VARCHAR(100) NOT NULL,                      -- 型号    grade ENUM('S', 'A', 'B', 'C') NOT NULL,      -- 车况    acquisition_price DECIMAL(10, 2) NOT NULL,        -- 收购价格    acquisition_date DATE NOT NULL,                   -- 收购日期    sale_price DECIMAL(10, 2),                        -- 销售价格（如果已售出）    sale_date DATE,                                   -- 销售日期（如果已售出）    status ENUM('available', 'sold') NOT NULL,        -- 状态：可售（available）或已售（sold）    dealer_id INT NOT NULL,                           -- 外键，车商ID    FOREIGN KEY (dealer_id) REFERENCES dealer(dealer_id)  -- 外键约束);

3.2项目总体设计架构

3.3初始化界面

1. 项目启动与用户身份验证：

当应用启动时，首先进入 MainActivity。

在 onCreate() 方法中，通过 SharedPreferences 检查是否存在已保存的用户信息（即车商的用户名、密码和用户ID）。如果存在，则说明用户已经登录过，直接跳转到车商主页面 (DealerMainActivity)。

如果没有保存用户信息，应用会跳转到登录页面 (DealerLoginActivity) 以供用户登录。

2. 数据加载与异步操作：

当用户已登录，应用会调用 GetCarList.getAvaiableList(id, this) 来加载该车商的可用车辆列表。getAvaiableList() 是一个通过 OkHttp 发起网络请求的异步方法，它向服务器请求该车商的车辆数据。数据返回后，应用会处理这些数据并展示在后续的页面上。

该请求和数据处理是异步执行的，不会阻塞主线程，因此不会影响界面的流畅性。

3. 异步处理与延时跳转：

在加载数据或进行其他操作时，应用使用 Handler().postDelayed() 进行延时处理，确保界面切换流畅。例如，登录验证成功后，延迟 3 秒跳转至主页面；如果没有登录信息，延迟 2 秒跳转至登录页面。

4. 用户界面与样式：

使用 Typeface.createFromAsset() 设置了自定义字体，使得界面呈现独特的风格。字体应用到两个 TextView 控件，提升了用户体验。

TODO:设计显示字体风格的步骤

1.将字体放在项目主目录下的assets下

2.创建Typeface类设置使用的字体

3.通过setTypeface方法设置Typeface设置的样式

Typeface typeface = Typeface.createFromAsset(getAssets(), "zhiti.ttf");text1.setTypeface(typeface);

3.4登录界面

在 DealerLoginActivity 中，我们实现了车商登录页面的相关功能。

主要功能：

登录验证：

用户输入用户名和密码进行登录，若输入为空，系统会提示“用户名或密码不能为空”。

输入合法后，系统会发送 HTTP 请求到后端进行登录验证。

跳转功能：

用户可以通过点击注册链接跳转到注册页面。

用户还可以选择点击“管理员登录”跳转到管理员登录页面。

数据存储：

登录成功后，将车商信息（用户名、密码、车商 ID）保存在 SharedPreferences 中，以便在后续的页面中使用。

后端验证：

向后端发送登录请求，返回登录结果。若登录成功，获取车商的 ID，并通过 ID 获取该车商的车辆列表。

错误提示：

若登录失败或请求出现问题，显示相应的错误信息，如“请求失败，请重试”。

详细分析

1. onCreate() 方法

设置了登录按钮的点击事件：

获取用户输入的用户名和密码。

如果有任意一个输入为空，弹出提示“用户名或密码不能为空”。

如果输入有效，调用 login(dealername, password) 方法进行登录验证。

设置了注册链接和管理员登录链接的点击事件：

点击注册链接跳转到注册页面 (DealerRegisterActivity)。

点击管理员登录链接跳转到管理员登录页面 (AdministratorLoginActivity)。

2. login() 方法

创建一个 POST 请求，将用户输入的用户名和密码传给后端验证。

使用 OkHttp 发送请求，并等待响应。

如果登录成功：

从响应中解析出车商信息（dealername、password、id）。

将这些信息存储到 SharedPreferences 中。

调用 GetCarList.getAvaiableList(id, DealerLoginActivity.this) 方法，获取该车商的车辆信息。

在成功获取车辆信息后，跳转到车商主页面 (DealerMainActivity)。

如果登录失败，显示相应的错误信息。

3. 布局文件（activity_dealer_login.xml）

布局文件设计了一个登录页面，主要包括以下控件：

TextView 控件显示标题“欢迎登录”。

两个 EditText 控件分别用于输入用户名和密码，inputType 分别设置为普通文本和密码文本。

一个 Button 控件作为登录按钮，点击后触发登录逻辑。

两个 TextView 控件分别用于显示注册链接和管理员登录链接。

TODO:登录请求与数据处理

关键步骤：

通过 OkHttp 发送 HTTP 请求，携带用户名和密码信息。

根据响应的数据判断登录是否成功。

成功则保存车商数据，获取车辆列表并跳转到主页面。

失败则显示错误提示。

private void login(String dealername, String password) {    // 创建 POST 请求    RequestBody formBody = new FormBody.Builder()    .addEncoded("dealername", dealername)    .addEncoded("password", password)    .build();    Request request = new Request.Builder()    .url("http://10.21.48.224:8080//dealer/login") // 替换为实际的 Servlet URL    .post(formBody)    .addHeader("Content-Type", "application/x-www-form-urlencoded; charset=UTF-8")    .build();    try {        OkHttpClient client = new OkHttpClient();        Response response = client.newCall(request).execute();        if (response.isSuccessful()) {            String responseData = response.body().string();            JSONObject responseJson = new JSONObject(responseData);            String msg = responseJson.optString("message", "ok");            if (msg.equals("ok")) {                String dealernameResponse = responseJson.getString("dealername");                String passwordResponse = responseJson.getString("dealerpassword");                id = responseJson.getInt("id");                sharedPreferences = getSharedPreferences("dealer_data", MODE_PRIVATE);                SharedPreferences.Editor editor = sharedPreferences.edit();                editor.putString("dealername", dealernameResponse);                editor.putString("dealerpassword", passwordResponse);                editor.putInt("dealerid", id);                editor.apply();                GetCarList.getAvaiableList(id, DealerLoginActivity.this);                runOnUiThread(() -&gt; {                    new Handler().postDelayed(() -&gt; {                        Intent intent = new Intent(DealerLoginActivity.this, DealerMainActivity.class);                        startActivity(intent);                        finish();                    }, 500);                });            } else {                runOnUiThread(() -&gt; Toast.makeText(DealerLoginActivity.this, msg, Toast.LENGTH_SHORT).show());            }        } else {            runOnUiThread(() -&gt; Toast.makeText(DealerLoginActivity.this, "请求失败请重试", Toast.LENGTH_SHORT).show());        }    } catch (IOException | JSONException e) {        runOnUiThread(() -&gt; Toast.makeText(DealerLoginActivity.this, "请求失败，请重试", Toast.LENGTH_SHORT).show());    }}

TODO:不希望那么快执行

在通过HTPP请求获取车商车辆信息时创建了一个线程来专门来处理，如果后面直接转跳到主页面，那么有可能因为数据还没有处理完，导致主页面什么数据都没有获取到，所以这里可以通过一下办法来解决这个问题

第一个参数是需要执行的代码，第二个参数是等待时间以毫秒为单位。

new Handler().postDelayed(() -&gt; {    Intent intent = new Intent(MainActivity.this, DealerMainActivity.class);    startActivity(intent);    finish();}, 3000);

3.5注册界面

在 DealerRegisterActivity 中，我们实现了车商注册页面的相关功能。

主要功能：

注册验证：

用户输入用户名和密码进行登录，若输入为空，系统会提示“用户名或密码不能为空”。

输入合法后，系统会发送 HTTP 请求到后端进行注册验证。

跳转功能：

用户可以通过点击注册链接跳转到登录页面。

数据存储：

注册成功后，将车商信息（用户名、密码、车商 ID）保存在 SharedPreferences 中，以便在后续的页面中使用。

后端验证：

向后端发送注册请求，返回结果。若注册成功，获取车商的 ID，并通过 ID 获取该车商的车辆列表。

错误提示：

若注册失败或请求出现问题，显示相应的错误信息，如“请求失败，请重试”。

两次输入密码不同给出提示“密码和确认密码不匹配”。

若有相同名字的车商注册则提示更换名字。

详细分析

1. onCreate() 方法

在 onCreate() 方法中，我们初始化了界面上的各个控件，并为注册按钮和登录跳转链接设置了点击事件的监听器。

控件初始化：使用 findViewById() 方法绑定界面元素（如商户名、密码、确认密码输入框，注册按钮和登录链接）。

按钮点击监听：当用户点击注册按钮时，获取用户输入的商户名、密码和确认密码，并进行验证。如果验证通过，则调用 regist() 方法发送注册请求；如果验证失败，显示相应的错误提示。

跳转到登录界面：当用户点击“已有账号，登录”链接时，系统会启动 DealerLoginActivity 并结束当前界面，便于用户进入登录页面。

2. regist() 方法

该方法用于向服务器发送注册请求，并处理服务器的响应结果。

构建 POST 请求：通过 FormBody.Builder 创建包含商户名称和密码的请求体，并使用 Request.Builder 构建 POST 请求，指定注册接口的 URL。

异步线程处理请求：为了避免主线程阻塞，网络请求操作放在子线程中执行。我们使用 OkHttpClient 发起请求，并获取服务器的响应。

解析响应：服务器返回的是一个 JSON 格式的数据，系统通过 JSONObject 解析该数据，获取商户名称、密码和商户 ID。如果注册成功，系统会将这些信息存储到本地 SharedPreferences 中，方便后续登录时使用；如果失败，系统会通过 Toast 提示用户具体的错误信息。

3. UI 更新与界面跳转

存储用户信息：注册成功后，系统将商户信息存储在 SharedPreferences 中，确保用户在下次启动应用时无需重新输入账号信息。

跳转至主界面：当注册成功并存储用户信息后，系统会使用 Intent 跳转到 DealerMainActivity，并结束当前的注册界面，确保用户无法通过返回键进入注册界面。

错误提示：如果网络请求失败或注册失败，系统会及时通过 Toast 提示用户，确保用户了解当前操作的结果。

4. 错误处理

网络错误处理：在进行网络请求时，若发生连接错误或响应失败，系统会通过 Toast 提示用户“请求失败请重试”，避免用户因网络问题产生困惑。

输入验证失败处理：在用户输入无效时，系统会实时反馈，确保用户知道自己的输入哪里出错，例如“所有字段都不能为空”或“密码和确认密码不匹配”。

TODO：动态修改界面必须在主线程中

若不在主线程中，那么可以通过runOnUiThread来修改界面

runOnUiThread(() -&gt; Toast.makeText(DealerRegisterActivity.this, msg, Toast.LENGTH_SHORT).show());

3.6用户信息修改或退出界面

在 DealerUserActivity 和 DealerEditActivity 中，我们实现了车商个人信息展示与编辑功能。主要包括展示用户基本信息、编辑个人信息、退出登录等核心功能。

主要功能

个人信息展示：在 DealerUserActivity 中，用户可以查看到自己的基本信息，如用户名。同时，提供编辑个人信息的入口，方便用户更新其账户信息。

编辑个人信息：用户通过点击“编辑个人信息”按钮进入 DealerEditActivity，在此界面用户可以修改用户名和密码。修改完成后，用户的信息会被更新到本地存储（SharedPreferences），并通过网络请求更新到服务器端。

退出登录：在 DealerUserActivity 中，提供了退出登录的按钮。当用户点击时，系统会清除本地存储的用户登录信息，跳转至登录页面，保证用户信息的安全性。

详细分析

1. onCreate() 方法 (DealerUserActivity)

在 onCreate() 方法中，我们首先初始化界面上的控件，展示用户的基本信息（如用户名）。然后，设置了两个按钮的点击事件：

编辑个人信息：点击后会启动 DealerEditActivity，让用户可以修改其个人信息。

退出登录：点击后，会调用 logout() 方法清除本地存储的用户信息，并跳转到登录页面。

2. logout() 方法 (DealerUserActivity)

logout() 方法负责清除存储的用户登录信息，确保用户退出登录后，其他用户无法获取到之前的账户信息。通过清空 SharedPreferences 中的 dealername、dealerpassword 和 dealerid，然后跳转至登录页面，结束当前活动。

3. onCreate() 方法 (DealerEditActivity)

在 DealerEditActivity 中，初始化用户的个人信息，并将其显示在界面上（如用户名和密码）。当用户修改了信息后，点击保存按钮，会调用 dealerEdit() 方法，将新信息发送到服务器进行更新。

初始化控件：获取并显示当前存储的用户信息。

点击保存按钮：验证用户输入是否修改，并通过调用 dealerEdit() 方法将修改后的信息提交给服务器。

4. dealerEdit() 方法 (DealerEditActivity)

dealerEdit() 方法用于将用户修改后的用户名和密码发送到服务器进行更新。通过 OkHttp 库发送 POST 请求，将新数据传输到服务器，服务器响应后，更新本地存储的用户信息。

网络请求：构建并发送包含新用户名和密码的 POST 请求到服务器。

响应处理：服务器返回更新结果后，判断更新是否成功，如果成功则更新本地存储的数据并跳转至主界面；如果失败，则弹出提示信息。

5. TODO：获取服务器的单个json数据

获取到服务器响应的数据后，将String转换为JSON格式的数据

1.创建JSONObject对象，并将响应的数据传递到其中

2.可以通过has方法来判断是否有这个数据

3.通过getxxx方法获得相对应的数据

JSONObject responseJson = new JSONObject(responseData);String dealernameResponse = null;String passwordResponse = null;int id = 0; String msg;if(responseJson.has("dealername")) dealernameResponse = responseJson.getString("dealername");if(responseJson.has("dealerpassword")) passwordResponse = responseJson.getString("dealerpassword");if(responseJson.has("id")) id = responseJson.getInt("id");if(responseJson.has("message")) msg = responseJson.getString("message");

3.7主界面（车商收购车辆展示）

在 DealerMainActivity 中，我们实现了车商主界面相关的功能，主要包括展示所有可用车辆、支持按品牌或型号进行车辆搜索、提供多个操作入口，如查看已售车辆、添加新车、编辑个人信息等。该界面作为车商操作的核心，通过简洁的界面布局与交互流程提升了用户体验。车辆数据通过本地数据库和网络请求结合方式获取，确保数据的实时性和完整性。

主要功能

车辆展示与搜索功能： 在主界面上，我们展示了所有可用车辆的列表，并提供了一个搜索栏，用户可以通过输入车辆品牌或型号来过滤展示的车辆。通过点击搜索按钮，系统会在本地数据库中查找匹配的车辆并展示出来。

导航功能： 主界面底部有多个按钮，分别用于：

查看已接收车辆：通过点击后展示所有已接收的车辆。

查看已售车辆：跳转到已售车辆页面。

添加新车：跳转到添加新车的页面。

查看个人信息：跳转到个人信息管理页面，便于车商编辑个人资料。

车辆列表展示： 车辆数据通过 RecyclerView 显示，采用网格布局显示车辆信息，以便车商浏览所有车辆。用户可根据需求进行筛选、查看。

数据存储与管理： 使用本地数据库（CarAvaiableManager）管理车辆数据，支持搜索、过滤功能，确保数据的实时性与完整性。通过网络请求更新车辆信息，保证车商信息同步更新。

详细分析

1. onCreate() 方法：

onCreate() 方法是 DealerMainActivity 的入口方法。在此方法中，我们初始化了各个控件（如搜索框、RecyclerView 和按钮）。同时，设置了各个导航按钮的点击事件，如：

搜索按钮：点击搜索按钮后，根据用户输入的品牌或型号查询本地数据库中的车辆，并将符合条件的车辆展示在 RecyclerView 中。

导航按钮：如查看已接收车辆、已售车辆、添加新车以及查看个人信息，点击后分别跳转到相应的界面。

2. showList() 方法：

showList() 方法用于展示所有可用的车辆列表。该方法通过 CarAvaiableManager 从本地数据库中获取所有的车辆信息，并将其传递给 CarAdapter 来展示。车辆数据会实时刷新，确保车商始终能查看到最新的车辆信息。

private void showList(int id) {    Car cars = new ArrayList&lt;&gt;();    CarAvaiableManager db = new CarAvaiableManager(DealerMainActivity.this);    cars = db.findAllAvailableCars();    if (db != null) {        db.closeDB();    }    Log.d("oooo",cars.toString());    runOnUiThread(() -&gt; {        carAdapter = new CarAdapter(cars,DealerMainActivity.this);        recyclerView.setAdapter(carAdapter);    });}

3. 搜索功能：

搜索功能通过用户输入的品牌或型号对车辆进行过滤。点击搜索按钮后，程序会通过 CarAvaiableManager 查询数据库，找到符合条件的车辆，并更新显示。搜索结果会实时更新到 RecyclerView 中。

searchBtn.setOnClickListener(view -&gt; {            String serch = search_bar.getText().toString();            CarAvaiableManager db = new CarAvaiableManager(DealerMainActivity.this);            cars = db.findAvailableCarByBrand(serch);            cars.addAll(db.findAvailableCarByModel(serch));            runOnUiThread(() -&gt; {                carAdapter = new CarAdapter(cars,DealerMainActivity.this);                recyclerView.setAdapter(carAdapter);            });            if (db != null) {                db.closeDB();            }        });

4. received 按钮点击事件：

该按钮触发时，通过 GetCarList.getAvaiableList() 获取所有可用车辆，并调用 showList() 方法来展示更新后的车辆数据。此功能用于查看所有接收的车辆信息。

5. 底部导航栏点击事件：

每个底部导航按钮都与特定的活动关联，点击后会启动相应的页面（如查看已售车辆、添加新车、查看个人资料等）。

6. TODO:如何显示两列列表

通过RecyclerView布局来实现

recyclerView.setLayoutManager(new GridLayoutManager(this, 2));

7. TODO：布局导入相同的布局

在此次项目中，有一个相同的下导航

先在layout中写好布局文件，然后再要显示的布局文件中导入就行

&lt;include layout="@layout/activity_bottom_navigation" /&gt;

再每一个导入布局文件的Activity中都需要重新再设置这些控件的动作

3.8车辆具体信息

在 CarViewActivity 中，我们实现了车辆详情页面的展示与操作功能。该功能主要围绕展示车辆的详细信息、出售车辆的操作流程以及交互设计展开。用户可以查看车辆的详细信息，选择出售车辆并记录销售价格和销售日期。整个界面通过与数据库和服务器的交互，确保了数据的准确性与实时性，同时提供了简洁且流畅的用户体验。

主要功能

车辆信息展示： 页面上展示了车辆的详细信息，包括品牌、收购价格、车辆的评估等级（S/A/B/C）、以及收购日期。车辆的评估等级通过不同的图片进行视觉展示。车辆的基本信息来自于本地数据库，确保了数据的快速加载与展示。

车辆出售操作： 用户可以通过点击“卖出”按钮来进入一个对话框，填写销售价格并选择销售日期。该操作会向后台发送请求，更新车辆的销售信息。如果操作成功，系统会更新车辆状态并返回车商主界面。

数据交互与更新： 车辆的详细信息通过数据库查询加载，而销售操作涉及到与服务器的通信，确保了车辆的销售记录能及时更新。销售信息包括销售价格和销售日期，通过网络请求提交给后台，成功后会刷新车辆列表并跳转到主界面。

交互设计： 通过清晰的按钮和弹出对话框设计，用户能够快速进行车辆的出售操作。页面布局简洁而直观，易于用户理解和操作。

详细分析

1. onCreate() 方法：

在此方法中，我们初始化了页面上的所有控件（如返回按钮、车辆信息显示区域、出售按钮等），并通过 getIntent() 方法获取传递过来的车辆 ID。之后，我们调用 loadCarDetails() 方法从本地数据库中加载该车辆的详细信息，并展示在界面上。

2. loadCarDetails(int carId) 方法：

该方法负责从数据库中根据车辆 ID 查询车辆信息。通过 CarAvaiableManager 从本地数据库加载该车辆的所有属性（如品牌、型号、评估等级、收购价格、收购日期）。如果查询到车辆信息，则通过 runOnUiThread() 方法更新 UI，显示车辆信息，并根据车辆的评估等级设置相应的图片资源。

3. 出售按钮的点击事件：

当用户点击“卖出”按钮时，会弹出一个对话框，用户可以在对话框中填写销售价格并选择销售日期。日期选择通过 DatePicker 控件实现，且最小选择日期为车辆的收购日期。用户填写信息后，点击提交按钮，系统会向后台发送一个包含车辆 ID、销售价格和销售日期的 POST 请求。如果请求成功，系统会刷新车辆列表并跳转到主界面。

4. 与后台交互的网络请求：

出售车辆的操作会发送一个 POST 请求到指定的 URL（后台接口）。请求的参数包括车辆 ID、销售价格和销售日期。请求成功后，返回的信息会被解析，并根据返回的消息显示相应的提示。如果操作成功，系统会通过 SharedPreferences 获取车商的 ID，并通过 GetCarList.getAvaiableList() 获取更新后的车辆列表。

5. 返回按钮的点击事件：

页面上有一个返回按钮，用户可以通过点击该按钮返回到上一页。returnImg.setOnClickListener(v -&gt; finish()) 方法通过调用 finish() 来结束当前活动并返回上一页。

6. TODO：动态设置图片路径

图片设置路径时其实传递发就是一串数值（int型），可以使用switch--case语句来设置路径

然后通过setImageResource方法来设置图片路径

if (car != null) {    String gradle = car.getGrade();    int imageResId;    switch (gradle) {        case "S":            imageResId = R.drawable.sgrade;            break;        case "A":            imageResId = R.drawable.agrade;            break;        case "B":            imageResId = R.drawable.bgrade;        case "C":            imageResId = R.drawable.cgrade;            break;        default:            imageResId = R.drawable.cgrade; // 默认图片            break;}gradeImg.setImageResource(imageResId);

7. TODO:弹窗设置

通过Dialog

1.通过构造方法创建Diaog对象，传递上下文

2.使用setContentView（）设置dialog的布局页面

3.写dialog中的Activity

4.通过show（）方法显示

Dialog dialog = new Dialog(CarViewActivity.this);dialog.setContentView(R.layout.dialog_car_sold);···dialog.show();

8. TODO：使用Android自带的时间控件，设置最小时间并获取用户选定的时间

使用DatePicker控件，datePickerMode设置样式，calendarViewShown设置不显示具体时间

动态设置最下时间setMinDate

获取时间getxxx，其中月份0表示1月

DatePicker datePicker = dialog.findViewById(R.id.stime);datePicker.setMinDate(car.getAcquisitionDate().getTime());String date = datePicker.getYear() + "-" + (datePicker.getMonth() + 1) + "-" + datePicker.getDayOfMonth();

3.9商家已卖车辆展示页面

在 DealerSoldActivity 和 CarSoldViewActivity 中，我们实现了展示和查看已售车辆的相关功能。

主要的功能：

展示已售车辆列表：在 DealerSoldActivity 中，使用 RecyclerView 展示所有已售车辆，并通过点击切换到售卖车辆的具体页面。

查看车辆详情：在 CarSoldViewActivity 中，根据传递的 carId，从服务器获取车辆的详细信息，包括收购价格、售卖价格和售卖时间，并展示这些信息。

处理用户点击事件：通过点击按钮和菜单项进行不同操作，如跳转到其他页面，查看车辆详情，或者更新车辆状态。

详细分析

1. onCreate()：

在 DealerSoldActivity 中，onCreate() 方法初始化了页面的各个组件（如按钮、RecyclerView），并处理了底部导航栏的点击事件。

在 CarSoldViewActivity 中，onCreate() 方法初始化了相关的界面元素，并根据传递的 carId 加载车辆详情。

2. showList()：

该方法用于从服务器获取已售车辆的列表，并展示到 RecyclerView 中。它通过调用 GetCarList.getSoldList(id, this) 来从远程服务器获取数据，并通过 CarSoldAdapter 将数据绑定到界面。

3. loadCarDetails()：

该方法用于根据传递的 carId 从服务器获取指定车辆的详细信息。使用 OkHttp 发起 POST 请求，返回的数据会被解析为 JSON 格式，并更新到界面的相应组件中，如车辆的品牌、收购价格、售卖价格等信息。

4. 按钮点击事件：

在 DealerSoldActivity 中，点击底部导航栏按钮可以切换不同的页面（如切换到 DealerUserActivity 页面，或跳转到 AddCarActivity 页面）。

在 CarSoldViewActivity 中，点击返回按钮会关闭当前页面，返回上一级页面。

5. 数据获取与更新：

在 showList() 和 loadCarDetails() 中，我们通过 HTTP 请求从服务器获取数据并更新界面。所有请求都在子线程中执行，并使用 runOnUiThread() 切换到主线程更新 UI。

3.10车辆添加

在 AddCarActivity 中，我们实现了一个车辆添加功能。该功能允许用户输入车辆的基本信息，包括发动机号、品牌、型号、价格以及车辆的等级，并将这些数据提交到服务器进行处理。主要的功能包括用户输入信息、选择车辆等级、提交数据到服务器并处理响应，最终跳转到主页面或显示错误提示。

主要功能

车辆信息输入： 用户可以通过界面上的输入框输入车辆的相关信息，如发动机号、品牌、型号和价格。这些信息是添加车辆的基础数据。

选择车辆等级： 通过 Spinner 控件，用户可以选择一个等级选项（如S级、A级等）。这决定了车辆的评估等级，并将作为提交的数据之一。

提交信息到服务器： 用户输入完毕后，点击“确定”按钮，系统会将输入的所有数据发送到服务器，通过 POST 请求将车辆信息提交。服务器返回的响应会告知用户是否成功添加车辆。

处理服务器响应： 如果提交成功，系统会显示一个确认信息，并且跳转回主页面。如果提交失败，系统会通过一个 Toast 提示用户重新确认信息。

详细分析

1. onCreate 方法：

初始化用户界面元素，包括输入框、按钮、选择框等。

设置 Spinner 控件的监听器，确保用户选择的等级能够正确存储在 grade 变量中。

为提交按钮设置点击事件，点击时会收集所有的输入数据，并构造一个 POST 请求。

2. setOnItemSelectedListener：

监听 Spinner 的选择事件，当用户选择某个等级时，将该等级保存在 grade 变量中，方便后续提交数据。

3. addCarBtn.setOnClickListener：

该方法为按钮设置点击事件，点击时，首先将用户输入的数据封装为 FormBody，然后发送 HTTP 请求到指定的 URL。

如果请求成功，解析服务器返回的数据，检查返回的消息。如果返回的消息为“ok”，则表示车辆添加成功，应用会通过 Handler 延迟跳转到主页面。

如果返回的消息不是“ok”，则通过 Toast 弹出提示，要求用户重新确认。

4. Request 和 OkHttpClient：

用于构建和发送 HTTP 请求。请求类型为 POST，包含车辆信息的表单数据。通过 OkHttpClient 实际发送请求，并获取响应数据。

5. Handler 和跳转：

在成功添加车辆后，使用 Handler 延时500毫秒后跳转到 DealerMainActivity 页面。runOnUiThread 方法确保 UI 更新操作在主线程中执行。

6. TODO：获取用户下拉框的所选的值

1.设置下拉框的值

2.再Activity中获取值

3.设置监听器setOnItemSelectedListener

Spinner level_spinner = findViewById(R.id.level_spinner);String[] levels = getResources().getStringArray(R.array.levels_array);level_spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {    @Override    public void onItemSelected(AdapterView&lt;?&gt; parent, View view, int position, long id) {        grade = levels[position];    }    @Override    public void onNothingSelected(AdapterView&lt;?&gt; adapterView) {    }});

四、重要代码分析

4.1从服务器获取Car列表

1. 发送网络请求

代码的第一部分构建了一个 POST 请求，发送到服务器以获取经销商的车辆列表：

RequestBody formBody = new FormBody.Builder().addEncoded("dealerId", Integer.toString(id)).build();

这里通过 FormBody.Builder() 创建了一个请求体，添加了 dealerId 参数，这个 ID 是从方法参数传入的。

addEncoded 方法确保参数会被正确编码（比如特殊字符不会被错误解析）。

然后，构建请求并指定请求方法为 POST：

Request request = new Request.Builder().url("http://10.21.48.224:8080//car/alist").post(formBody).addHeader("Content-Type", "application/x-www-form-urlencoded; charset=UTF-8").build();

url()：设置请求的目标 URL，这里是一个用于获取车辆列表的 API 地址。

post(formBody)：指定 HTTP 方法为 POST，并将请求体传递给它。

addHeader()：添加请求头，指定内容类型为表单 URL 编码格式。

然后，使用 OkHttpClient 发送请求：

OkHttpClient client = new OkHttpClient();Response response = client.newCall(request).execute();

通过 OkHttpClient 的 newCall(request) 方法发起网络请求。

execute() 方法同步等待响应，返回一个 Response 对象。

2. 请求结果处理

检查响应是否成功：

if (response.isSuccessful()) {    // 请求成功} else {    // 请求失败}

isSuccessful() 判断请求是否成功，成功返回 200-299 范围的 HTTP 状态码。

如果成功，开始解析服务器返回的数据：

String responseData = response.body().string();JSONArray jsonArray = new JSONArray(responseData);

response.body().string() 获取响应体的字符串内容，即车辆信息的 JSON 格式数据。

使用 JSONArray 将返回的 JSON 字符串转换为一个 JSON 数组对象。

3. 解析 JSON 数据

遍历 JSONArray，将每个元素解析成 Car 对象：

for (int i = 0; i &lt; jsonArray.length(); i++) {    JSONObject jsonObject = jsonArray.getJSONObject(i);    Car car = new Car();    car.setCarId(jsonObject.getInt("carId"));    car.setEngineNumber(jsonObject.getString("engineNumber"));    car.setBrand(jsonObject.getString("brand"));    car.setModel(jsonObject.getString("model"));    car.setGrade(jsonObject.getString("grade"));    car.setAcquisitionPrice(jsonObject.getDouble("acquisitionPrice"));    Date acquisitionDate = new SimpleDateFormat("yyyy-MM-dd").parse(jsonObject.getString("acquisitionDate"));    car.setAcquisitionDate(acquisitionDate);    car.setStatus(jsonObject.getString("status"));    car.setDealerId(jsonObject.getInt("dealerId"));    cars.add(car);}

jsonArray.getJSONObject(i)：获取数组中的每个 JSONObject，即每一辆车的详细数据。

对每个 JSONObject 进行解析，使用 getString(), getInt(), getDouble() 等方法提取字段值并设置到 Car 对象中。

对于日期字段（acquisitionDate），使用 SimpleDateFormat 将其从字符串转为 Date 类型。

最后，将解析出的 Car 对象添加到 cars 列表中。

4. 将数据保存到数据库

解析后的车辆信息需要保存到数据库中，使用 CarAvaiableManager 来进行数据库操作：

CarAvaiableManager db = new CarAvaiableManager(context);db.addCar(cars);if (db != null) {    db.closeDB();}

CarAvaiableManager 是一个自定义的数据库管理类，用于操作本地数据库。

addCar(cars) 方法将所有解析得到的 Car 对象插入到数据库。

操作完毕后，通过 closeDB() 关闭数据库连接。

代码中的关键步骤总结

发送请求：创建请求体并发送 HTTP POST 请求到服务器。

使用 FormBody 创建请求体，添加请求参数。

创建 Request 对象并设置 URL 和请求头。

使用 OkHttpClient 发送请求并接收响应。

解析响应：将 JSON 格式的响应数据解析成 Car 对象。

获取响应数据并将其转换为 JSON 数组。

遍历 JSON 数组，将每个元素转换成 Car 对象。

设置 Car 对象的各个属性，包括处理日期字段。

4.2SqlLit操作

4.2.1获取数据库连接

实现了一个 SQLiteOpenHelper 子类 CarAvaiableHelper，用于管理与数据库的连接、创建和更新。它主要负责用于存储车辆信息的数据库表的创建、版本控制等操作。

1. 类的作用与构造方法

CarAvaiableHelper 继承了 SQLiteOpenHelper，这个类的主要作用是帮助我们管理数据库的创建和版本控制。它有两个主要功能：

创建数据库：通过 onCreate() 方法定义数据库结构。

升级数据库：当数据库版本号更新时，onUpgrade() 方法会被调用。

public class CarAvaiableHelper extends SQLiteOpenHelper {private static final String DB_NAME = "oraolausedcar.db";private static final int DB_VERSION = 2;public static final String TABLE_NAME = "car_available";

DB_NAME：数据库文件的名称。

DB_VERSION：数据库的版本号，用于控制数据库升级。

TABLE_NAME：数据库表名，在这个类中是 car_available，用于存储车辆信息。

getInstance() 是一个静态方法，用于返回 CarAvaiableHelper 的实例：

public static CarAvaiableHelper getInstance(Context context, int version){    return new CarAvaiableHelper(context, DB_NAME, null, version);}

这个方法接受 Context 和 version 参数，并返回 CarAvaiableHelper 的实例。可以根据需要传入不同的版本号来控制数据库升级。

构造方法：

public CarAvaiableHelper(@Nullable Context context, @Nullable String name, @Nullable SQLiteDatabase.CursorFactory factory, int version) {    super(context, name, factory, version);}

调用父类的构造方法，SQLiteOpenHelper 会帮助管理数据库的创建和升级。

2. 数据库创建

onCreate() 方法负责数据库的初始化。在这里，我们定义了数据库的表结构：

@Overridepublic void onCreate(SQLiteDatabase sqLiteDatabase) {String sql = "CREATE TABLE IF NOT EXISTS " + TABLE_NAME +" (carId INTEGER PRIMARY KEY, engineNumber VARCHAR NOT NULL, brand VARCHAR NOT NULL, model VARCHAR NOT NULL, grade VARCHAR NOT NULL, " +"acquisitionPrice DECIMAL(10, 2) NOT NULL , acquisition_date VARCHAR NOT NULL);";sqLiteDatabase.execSQL(sql);}

3. 数据库更新

onUpgrade() 方法负责数据库升级的操作。它会在数据库版本更新时被调用：

@Overridepublic void onUpgrade(SQLiteDatabase sqLiteDatabase, int oldVersion, int newVersion) {    String sql = "DROP TABLE IF EXISTS " + TABLE_NAME;    sqLiteDatabase.execSQL(sql);    onCreate(sqLiteDatabase);}

删除旧表：如果数据库版本升级了（oldVersion != newVersion），则首先通过 DROP TABLE IF EXISTS 删除旧的表。

重新创建表：然后调用 onCreate() 方法重新创建表结构。

设计思路

这个类主要通过继承 SQLiteOpenHelper 类来管理数据库的创建和更新，设计上有以下几个特点：

数据库管理：利用 SQLiteOpenHelper 提供的接口，简化数据库的管理操作（创建、升级）。

表的设计：表结构简单，适合存储车辆信息，每辆车的数据包括 carId、engineNumber、brand、model、grade、acquisitionPrice、acquisition_date 等信息。

版本控制：通过设置 DB_VERSION 实现数据库的版本管理，确保在版本变化时能够正确处理表结构的变化。

代码中的关键步骤总结

创建数据库：

创建 CarAvaiableHelper 类的实例。

调用 onCreate() 方法定义数据库表结构。

升级数据库：

当数据库版本更新时，onUpgrade() 方法会被自动调用，执行删除旧表并重新创建新表。

获取数据库实例：

通过 getInstance() 获取 CarAvaiableHelper 的实例。

4.2.2数据库操作

这段代码实现了一个名为 CarAvaiableManager 的类，负责操作与车辆信息相关的数据库操作。它通过 SQLiteDatabase 来进行数据库的增、删、查、改操作，并在执行这些操作时使用事务管理来确保数据的完整性和一致性。

1. 类的构造方法和数据库连接

public CarAvaiableManager(Context context) {CarAvaiableHelper carAvaiableHelper = CarAvaiableHelper.getInstance(context, DB_VERSION);db = carAvaiableHelper.getWritableDatabase();}

CarAvaiableManager 的构造方法中，首先获取 CarAvaiableHelper 的实例，并使用它来获取可写的数据库实例 (db)，用于后续的数据库操作。

2. 查询所有可用车辆

public List&lt;Car&gt; findAllAvailableCars() {    db.beginTransaction();    try {        List&lt;Car&gt; cars = new ArrayList&lt;&gt;();        Cursor cursor = db.rawQuery("SELECT * FROM " + TABLE_NAME, null);        while (cursor.moveToNext()) {            Car car = new Car();            car.setCarId(cursor.getInt(cursor.getColumnIndex("carId")));            car.setEngineNumber(cursor.getString(cursor.getColumnIndex("engineNumber")));            car.setBrand(cursor.getString(cursor.getColumnIndex("brand")));            car.setModel(cursor.getString(cursor.getColumnIndex("model")));            car.setGrade(cursor.getString(cursor.getColumnIndex("grade")));            car.setAcquisitionPrice(cursor.getDouble(cursor.getColumnIndex("acquisitionPrice")));            car.setAcquisitionDate(new SimpleDateFormat("yyyy-MM-dd").parse(cursor.getString(cursor.getColumnIndex("acquisition_date"))));            cars.add(car);        }        cursor.close();        db.setTransactionSuccessful();        return cars;    } catch (ParseException e) {        throw new RuntimeException(e);    } finally {        db.endTransaction();    }}

这个方法查询数据库中所有可用的车辆信息。

通过 rawQuery 执行 SQL 查询，结果存储在 Cursor 中。每次移动 Cursor 到下一行数据时，就从中提取每个字段，并将其封装到 Car 对象中。

使用事务 (beginTransaction, setTransactionSuccessful, endTransaction) 确保操作的原子性。

3. 查询指定品牌的车辆

public List&lt;Car&gt; findAvailableCarByBrand(String brand) {db.beginTransaction();try {    List&lt;Car&gt; cars = new ArrayList&lt;&gt;();    Cursor cursor = db.rawQuery("SELECT * FROM " + TABLE_NAME + " WHERE brand LIKE ?", new String[]{"%" + brand + "%"});    while (cursor.moveToNext()) {        Car car = new Car();        car.setCarId(cursor.getInt(cursor.getColumnIndex("carId")));        car.setEngineNumber(cursor.getString(cursor.getColumnIndex("engineNumber")));        car.setBrand(cursor.getString(cursor.getColumnIndex("brand")));        car.setModel(cursor.getString(cursor.getColumnIndex("model")));        car.setGrade(cursor.getString(cursor.getColumnIndex("grade")));        car.setAcquisitionPrice(cursor.getDouble(cursor.getColumnIndex("acquisitionPrice")));        car.setAcquisitionDate(new SimpleDateFormat("yyyy-MM-dd").parse(cursor.getString(cursor.getColumnIndex("acquisition_date"))));        cars.add(car);    }    cursor.close();    db.setTransactionSuccessful();    return cars;} catch (ParseException e) {    throw new RuntimeException(e);} finally {    db.endTransaction();}}

通过品牌 (brand) 查询可用的车辆。LIKE 关键字用来模糊匹配品牌名，% 表示通配符。

同样使用事务来管理操作。

4. 查询指定型号的车辆

public List&lt;Car&gt; findAvailableCarByModel(String model) {db.beginTransaction();try {    List&lt;Car&gt; cars = new ArrayList&lt;&gt;();    Cursor cursor = db.rawQuery("SELECT * FROM " + TABLE_NAME + " WHERE model LIKE ?", new String[]{"%" + model + "%"});    while (cursor.moveToNext()) {        Car car = new Car();        car.setCarId(cursor.getInt(cursor.getColumnIndex("carId")));        car.setEngineNumber(cursor.getString(cursor.getColumnIndex("engineNumber")));        car.setBrand(cursor.getString(cursor.getColumnIndex("brand")));        car.setModel(cursor.getString(cursor.getColumnIndex("model")));        car.setGrade(cursor.getString(cursor.getColumnIndex("grade")));        car.setAcquisitionPrice(cursor.getDouble(cursor.getColumnIndex("acquisitionPrice")));        car.setAcquisitionDate(new SimpleDateFormat("yyyy-MM-dd").parse(cursor.getString(cursor.getColumnIndex("acquisition_date"))));        cars.add(car);    }    cursor.close();    db.setTransactionSuccessful();    return cars;} catch (ParseException e) {    throw new RuntimeException(e);} finally {    db.endTransaction();}}

通过车型 (model) 查询可用的车辆，同样使用 LIKE 进行模糊匹配。

5. 通过车 ID 查询指定车辆

public Car findAvailableCarByCarId(int carid) {db.beginTransaction();try {    Car car = new Car();    Cursor cursor = db.rawQuery("SELECT * FROM " + TABLE_NAME + " WHERE carId = ?", new String[]{Integer.toString(carid)});    if (cursor.moveToNext()) {        car.setCarId(cursor.getInt(cursor.getColumnIndex("carId")));        car.setEngineNumber(cursor.getString(cursor.getColumnIndex("engineNumber")));        car.setBrand(cursor.getString(cursor.getColumnIndex("brand")));        car.setModel(cursor.getString(cursor.getColumnIndex("model")));        car.setGrade(cursor.getString(cursor.getColumnIndex("grade")));        car.setAcquisitionPrice(cursor.getDouble(cursor.getColumnIndex("acquisitionPrice")));        car.setAcquisitionDate(new SimpleDateFormat("yyyy-MM-dd").parse(cursor.getString(cursor.getColumnIndex("acquisition_date"))));    }    cursor.close();    db.setTransactionSuccessful();    return car;} catch (ParseException e) {    throw new RuntimeException(e);} finally {    db.endTransaction();}}

通过车辆 ID (carId) 查询指定车辆，若查询到该车辆则返回其信息。

6. 添加车辆信息

public void addCar(List&lt;Car&gt; cars) {    try {        dropValues();        for (Car car : cars) {            db.execSQL("INSERT INTO " + TABLE_NAME + " VALUES(?,?,?,?,?,?,?)", new Object[]{car.getCarId(), car.getEngineNumber(), car.getBrand(), car.getModel(), car.getGrade(), car.getAcquisitionPrice(), new SimpleDateFormat("yyyy-MM-dd").format(car.getAcquisitionDate())});        }    } catch (Exception e) {        throw new RuntimeException(e);    }}

该方法将传入的车辆列表 (cars) 插入到数据库中。

使用 dropValues() 方法清空现有的车辆记录，确保添加的是最新的车辆数据。

7. 删除所有车辆数据

private void dropValues() {    db.beginTransaction();    try {        db.execSQL("DELETE FROM " + TABLE_NAME);        db.setTransactionSuccessful();    } finally {        db.endTransaction();    }}

清空表中的所有车辆数据，确保数据一致性。

8. 关闭数据库连接

public void closeDB() {    db.close();}

关闭数据库连接，释放资源。

设计思路

数据库操作封装：该类封装了与数据库的操作，包括查询、插入和删除等。通过 SQLiteDatabase 的事务管理（beginTransaction, setTransactionSuccessful, endTransaction）来确保操作的原子性。

灵活查询：提供了多种查询方法（如按品牌、按型号、按车 ID 查询），支持灵活的数据检索。

数据管理：支持批量插入和删除操作，通过 dropValues() 清空车辆表中的所有数据。

4.3适配器

实现了一个 CarAdapter 类，继承自 RecyclerView.Adapter，用于在 RecyclerView 中展示车辆列表。它负责将 Car 对象的数据展示到视图中，并处理用户交互。

1. 构造方法

public CarAdapter(List&lt;Car&gt; carList) {    this.carList = carList;}public CarAdapter(List&lt;Car&gt; carList, Context context) {    this.carList = carList;    this.context = context;}

第一个构造方法接收一个 List&lt;Car&gt;，用于初始化车辆列表。

第二个构造方法除了接收 List&lt;Car&gt; 之外，还接收一个 Context，用于启动新的 Activity（在 onBindViewHolder 中用到）。

2. onCreateViewHolder 方法

@NonNull@Overridepublic CarViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_list, parent, false);return new CarViewHolder(view);}

该方法在 RecyclerView 初始化时调用，用于创建 ViewHolder。这里使用了 LayoutInflater 来加载布局文件 item_list.xml，并返回一个新的 CarViewHolder 对象。

CarViewHolder 是一个静态内部类，表示每个条目的视图控件集合。

3. onBindViewHolder 方法

@Overridepublic void onBindViewHolder(@NonNull CarViewHolder holder, int position) {Log.d("qqqqqqqqqqqqq", "okokokokokokokokokokokokok");Car car = carList.get(position);// 处理品牌和型号，防止字段为 nullString brandModel = (car.getBrand() != null ? car.getBrand() : "Unknown") +(car.getModel() != null ? car.getModel() : " Model");holder.brandTextView.setText(brandModel);// 处理价格，防止字段为 nullString price = "￥" + car.getAcquisitionPrice();holder.priceTextView.setText(price);// 处理日期，防止字段为 nullString date = car.getAcquisitionDate() != null ?new SimpleDateFormat("yyyy-MM-dd").format(car.getAcquisitionDate()) :"Unknown Date";holder.createTimeTextView.setText(date);holder.itemView.setOnClickListener(v -&gt; {    Intent intent = new Intent(context, CarViewActivity.class);    intent.putExtra("carId", car.getCarId());    context.startActivity(intent);});}

数据绑定：根据 position 获取当前的 Car 对象，并将其字段值绑定到视图元素（如 TextView）上。

品牌与型号：如果品牌或型号为 null，则使用默认值 "Unknown" 或 "Model"。

价格：将车辆的 acquisitionPrice 以 "￥" 开头的字符串形式展示。

日期：将日期格式化为 "yyyy-MM-dd"，如果日期为 null，则显示为 "Unknown Date"。

点击事件：当用户点击某个列表项时，触发 OnClickListener，启动 CarViewActivity 并传递 carId。

4. getItemCount 方法

@Overridepublic int getItemCount() {    return carList.size();}

该方法返回数据集的大小，即 carList 的元素数量，决定了 RecyclerView 中的条目数量。

5. CarViewHolder 类

static class CarViewHolder extends RecyclerView.ViewHolder {    TextView brandTextView;    TextView priceTextView;    TextView createTimeTextView;    public CarViewHolder(@NonNull View itemView) {        super(itemView);        brandTextView = itemView.findViewById(R.id.brand);        priceTextView = itemView.findViewById(R.id.price);        createTimeTextView = itemView.findViewById(R.id.create_time);    }}

ViewHolder 用于缓存 RecyclerView 中每个条目的视图组件。通过 findViewById 获取对应的 TextView 控件，用于显示车辆品牌、价格和创建日期。

设计思路

适配器模式：通过 CarAdapter 继承自 RecyclerView.Adapter，将数据 (carList) 和视图绑定，实现了数据驱动的视图更新。

ViewHolder 缓存：使用 CarViewHolder 类缓存每个列表项中的视图组件，避免了频繁的 findViewById，提高了性能。

灵活的点击事件处理：通过 setOnClickListener 处理用户点击事件，点击后启动新的 Activity（CarViewActivity），并将对应的 carId 传递过去。

空值处理：在绑定数据时，考虑了字段可能为 null 的情况，避免了因 null 值导致的程序崩溃。

代码中的关键步骤总结

创建适配器实例：

使用车辆列表 (carList) 创建 CarAdapter 实例。可选择性地传入 Context 以便在适配器中处理点击事件。

将适配器设置给 RecyclerView：

使用 RecyclerView.setAdapter() 将 CarAdapter 设置给 RecyclerView，展示车辆列表。

在 onBindViewHolder 中绑定数据：

在适配器的 onBindViewHolder 方法中，将 Car 对象的数据（品牌、型号、价格、日期）绑定到视图上。

处理点击事件：

在 onBindViewHolder 中为每个条目设置点击监听器，点击时通过 Intent 启动 CarViewActivity 并传递必要的参数（如 carId）。

显示数据：

RecyclerView 根据 getItemCount 的返回值，动态显示车辆列表中的数据项。

五、项目总结

本次项目是我写的第四个大项目了，再看到题目的时候，我就想了本项目的架构，刚开始感觉非常的复杂，其实和我之前总结的一样，一步一步的来，先从简单的开始，然后一步一步的去实现就行，只要开了头就没有解决不了的问题。编写项目时有锻炼了许多知识，例如本次服务器使用的servlet，其中编译使用的Maven,当时在学习的时候感觉没有什么，可是真当要使用到复杂项目时，会发现有很多意想不到的问题，由于本次以Android为主，这里就不具体介绍了，之前在写报告解释代码后并不会去总结一些普遍常见的操作的基本步骤，本次实验报告写了相关问题的解决办法的基本步骤，这样以后遇到同样的需求的时候，就会更快。虽然上课的时间很短，可是学习Android的时候感觉并不难，表明计算机其实有很多东西都是相通的，学习的时候要学会类比，这个技术和之前学习的技术有什么相同的、差异的。

当然本次项目也有许多的缺陷与不足，例如还有一个bug没有解决，在一个项目中不能操作两个表，现在还没有找到是哪里的问题；修改信息其实应该先将sqllite中的修改了然后再在合适的时间将修改的信息传递给服务器；在展示列表中的数据是一回传递过来的，如果数据量大应该分几次传递；上拉下拉没有实现，其实都找好了方法SwipeRefreshLayout；统计功能没有实现可以使用MpAndroidChart。其实这样也很好在规定的时间内完成基本的功能，要有取舍。

希望以后有时间能填补以上没有完成的坑，同时在本项目中的一些学习方法也应该保持，加油！

2
