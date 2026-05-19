<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منصة الاختبارات الذكية - مبادئ الإدارة</title>
    <style>
        :root {
            --primary: #1e3a8a; /* أزرق داكن */
            --secondary: #3b82f6; /* أزرق فاتح */
            --bg-color: #f3f4f6;
            --card-bg: #ffffff;
            --text-main: #1f2937;
            --success: #10b981; /* أخضر */
            --danger: #ef4444; /* أحمر */
            --warning: #f59e0b; /* برتقالي */
        }
        body {
            font-family: 'Segoe UI', Tahoma, Arial, sans-serif;
            background-color: var(--bg-color);
            margin: 0;
            padding: 0;
            color: var(--text-main);
        }
        .header {
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            color: white;
            text-align: center;
            padding: 30px 20px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        .header h1 {
            margin: 0;
            font-size: 28pt;
        }
        .header p {
            margin-top: 10px;
            font-size: 14pt;
            opacity: 0.9;
        }
        .container {
            max-width: 900px;
            margin: 40px auto;
            padding: 0 20px;
        }
        /* تصميم الأسئلة */
        .question-card {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 25px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            border-right: 6px solid var(--secondary);
            transition: transform 0.2s ease;
        }
        .question-card:hover {
            transform: translateY(-3px);
        }
        .q-text {
            font-size: 16pt;
            font-weight: bold;
            color: var(--primary);
            margin-top: 0;
            margin-bottom: 20px;
        }
        .options-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 15px;
        }
        .option-btn {
            background-color: #f8fafc;
            border: 2px solid #e2e8f0;
            color: var(--text-main);
            padding: 15px;
            font-size: 14pt;
            border-radius: 8px;
            cursor: pointer;
            text-align: right;
            transition: all 0.2s;
            font-family: inherit;
        }
        .option-btn:hover:not(:disabled) {
            background-color: #e2e8f0;
            border-color: #cbd5e1;
        }
        .option-btn.selected {
            background-color: #dbeafe;
            border-color: var(--secondary);
            font-weight: bold;
        }
        /* ألوان التصحيح النهائي */
        .option-btn.correct-ans {
            background-color: var(--success) !important;
            color: white !important;
            border-color: #059669 !important;
        }
        .option-btn.wrong-ans {
            background-color: var(--danger) !important;
            color: white !important;
            border-color: #b91c1c !important;
        }
        /* الأزرار والنتائج */
        .controls {
            text-align: center;
            margin: 40px 0;
        }
        .btn-submit {
            background-color: var(--primary);
            color: white;
            border: none;
            padding: 15px 50px;
            font-size: 18pt;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(30, 58, 138, 0.4);
            transition: all 0.3s ease;
        }
        .btn-submit:hover {
            background-color: #172554;
            transform: scale(1.05);
        }
        .btn-reload {
            background-color: var(--warning);
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: 16pt;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            margin-top: 20px;
            display: none;
            margin-left: auto;
            margin-right: auto;
        }
        .result-board {
            background: var(--card-bg);
            border-radius: 15px;
            padding: 40px;
            text-align: center;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            display: none;
            margin-bottom: 40px;
            border-top: 8px solid var(--success);
        }
        .result-board h2 {
            font-size: 32pt;
            margin: 0 0 10px 0;
            color: var(--primary);
        }
        .result-board .level {
            font-size: 24pt;
            font-weight: bold;
            color: var(--warning);
            margin: 20px 0;
        }
        .progress-text {
            font-size: 14pt;
            color: #64748b;
        }
    </style>
</head>
<body>

    <div class="header">
        <h1>منصة الاختبارات الذكية - مبادئ الإدارة</h1>
        <p>يتم سحب 20 سؤالاً عشوائياً من بنك الأسئلة الضخم في كل مرة تختبر فيها 🔄</p>
    </div>

    <div class="container" id="quiz-container">
        </div>

    <div class="controls">
        <button class="btn-submit" id="submit-btn" onclick="evaluateExam()">إنهاء الاختبار وعرض النتيجة 📊</button>
    </div>

    <div class="container">
        <div class="result-board" id="result-board">
            <h2>نتيجتك: <span id="score-percent">0</span>%</h2>
            <div class="progress-text">لقد أجبت بشكل صحيح على <span id="score-points">0</span> من أصل <span id="total-points">20</span> سؤالاً.</div>
            <div class="level" id="user-level">المستوى: --</div>
            <button class="btn-reload" id="reload-btn" style="display: block;" onclick="location.reload()">توليد اختبار جديد بأسئلة مختلفة 🔄</button>
        </div>
    </div>

    <script>
        // بنك الأسئلة الضخم (50 سؤال منوع من كل فصول المادة)
        const fullQuestionBank = [
            // الفصل الأول
            { q: "تعريف الإدارة بأنها 'أداء الأشياء بطريقة صحيحة بأقل هدر للموارد' يعبر عن:", options: ["الفاعلية", "الكفاءة", "التخطيط", "التوجيه"], answer: 1 },
            { q: "المهارات الفكرية والإدراكية تزداد الحاجة إليها بشكل أكبر في مستوى:", options: ["الإدارة الوسطى", "الإدارة الدنيا", "الإدارة العليا", "الإدارة الإشرافية"], answer: 2 },
            { q: "تحويل الخطط الإستراتيجية طويلة الأجل إلى خطط تكتيكية هو من مهام:", options: ["الإدارة العليا", "الإدارة الوسطى", "الإدارة الدنيا", "العمال"], answer: 1 },
            { q: "الإدارة تعتبر علماً وفناً معاً في نفس الوقت.", options: ["صح", "خطأ"], answer: 0 },
            { q: "الفاعلية تعني أداء الأشياء الصحيحة وتحقيق الهدف المطلوب.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل الثاني (المدارس)
            { q: "مؤسس نظرية 'الإدارة العلمية' الذي اعتمد على دراسة الوقت والحركة هو:", options: ["ماكس فيبر", "إلتون مايو", "فريدريك تايلور", "هنري فايول"], answer: 2 },
            { q: "مبدأ 'وحدة الأمر' الذي ينص على تلقي الأوامر من رئيس واحد فقط، جاء به العالم:", options: ["هنري فايول", "ويليام أوشي", "دوجلاس ماكريجور", "تايلور"], answer: 0 },
            { q: "النظرية التي تفترض أن الإنسان بطبعه كسول ويكره العمل وتحتاج لرقابة صارمة هي:", options: ["نظرية Z", "نظرية Y", "نظرية X", "إدارة الجودة الشاملة"], answer: 2 },
            { q: "أثبتت 'تجارب هوثورن' أن العوامل المادية كالإضاءة هي المحدد الأول للإنتاجية.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، بل العوامل النفسية
            { q: "المدرسة البيروقراطية لماكس فيبر تركز على التسلسل الهرمي الصارم وفصل العلاقات الشخصية.", options: ["صح", "خطأ"], answer: 0 },
            { q: "فلسفة 'الهندرة' تعني التحسين المستمر والبطيء للعمليات.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، الهندرة تعني تغيير جذري سريع
            { q: "نظرية Z اليابانية تركز على التوظيف مدى الحياة والقرار الجماعي.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل الثالث (البيئة)
            { q: "المنافسون، الزبائن، والموردون يعتبرون جزءاً من البيئة:", options: ["الداخلية للمنظمة", "الخارجية العامة", "الخارجية الخاصة (بيئة المهمة)", "التكنولوجية"], answer: 2 },
            { q: "الطقوس والرموز والقصص تعتبر جزءاً من الثقافة التنظيمية:", options: ["الجوهرية", "المرئية", "الخارجية", "السياسية"], answer: 1 },
            { q: "العوامل الاقتصادية كالتضخم تعتبر من عناصر البيئة الخارجية الخاصة (بيئة المهمة).", options: ["صح", "خطأ"], answer: 1 }, // خطأ، هي عامة
            { q: "الزبون الداخلي هو الموظف الذي يعتمد في عمله على مخرجات قسم آخر داخل الشركة.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل الرابع (التخطيط والقرارات)
            { q: "أوامر ونواهٍ صارمة لا تقبل الاجتهاد والمرونة (مثل: ممنوع التدخين) تسمى:", options: ["إجراءات", "سياسات", "أهداف", "قواعد"], answer: 3 },
            { q: "الخطط الإستراتيجية التي تضعها الإدارة العليا غالباً ما تكون:", options: ["طويلة الأجل", "متوسطة الأجل", "قصيرة الأجل", "يومية"], answer: 0 },
            { q: "القرارات التي تتخذ لحل مشكلات روتينية متكررة ولها لوائح جاهزة هي:", options: ["قرارات غير مبرمجة", "قرارات مبرمجة", "قرارات استراتيجية", "قرارات استثنائية"], answer: 1 },
            { q: "التخطيط التشغيلي قصير الأجل تضعه الإدارة الوسطى.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، تضعه الإدارة الدنيا
            { q: "من عيوب اتخاذ القرار الجماعي 'التفكير الجماعي السلبي' وتشتت المسؤولية.", options: ["صح", "خطأ"], answer: 0 },
            { q: "اتخاذ القرارات يعتبر جوهر ومحور العملية الإدارية.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل الخامس (التنظيم والتنسيق)
            { q: "عدد المرؤوسين الذين يمكن لمدير واحد الإشراف عليهم بكفاءة يسمى:", options: ["المركزية", "التسلسل الهرمي", "نطاق الإشراف", "التفويض"], answer: 2 },
            { q: "حصر واحتفاظ الإدارة العليا بسلطة اتخاذ القرارات يسمى:", options: ["اللامركزية", "المركزية", "التنسيق", "التنظيم المفتوح"], answer: 1 },
            { q: "الوظيفة التي تمنع التضارب والازدواجية بين الأقسام هي:", options: ["التوجيه", "الرقابة", "التنسيق", "التخطيط"], answer: 2 },
            { q: "اللامركزية تعني تفويض السلطات للمستويات الإدارية الأدنى لتسريع الإنجاز.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل السادس (التوجيه والاتصال)
            { q: "تعمد المرسل فلترة المعلومات وتعديلها لتظهر بشكل إيجابي للإدارة يسمى:", options: ["الضوضاء", "عامل اللغة", "التصفية (Filtering)", "التغذية الراجعة"], answer: 2 },
            { q: "كثرة المستويات الإدارية في الهيكل التنظيمي يعتبر من معوقات الاتصال:", options: ["النفسية", "التنظيمية", "اللغوية", "الشخصية"], answer: 1 },
            { q: "استخدام مصطلحات معقدة فنياً يعتبر معوقاً من معوقات الاتصال الإداري.", options: ["صح", "خطأ"], answer: 0 },
            
            // الفصل السابع (الرقابة)
            { q: "الرقابة التي تتم 'قبل' البدء الفعلي في التنفيذ لمنع الأخطاء هي رقابة:", options: ["متزامنة", "لاحقة", "سلبية", "سابقة (وقائية)"], answer: 3 },
            { q: "أداة بيانية رقابية تستخدم مستطيلات بيضاء للمخطط ومظللة للمنجز هي:", options: ["شجرة القرارات", "خريطة جانت (Gantt Chart)", "نظرية Z", "الهيكل الهرمي"], answer: 1 },
            { q: "النظام الرقابي المغلق يعتمد كلياً على تدخل العنصر البشري في التصحيح.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، المفتوح هو البشري
            { q: "الرقابة اللاحقة تفيد في تقويم النتائج بعد انتهائها للاستفادة منها مستقبلاً.", options: ["صح", "خطأ"], answer: 0 },
            { q: "مدى ولاء الموظفين ورضا الزبائن يعتبر من المعايير الرقابية الكمية (الرقمية).", options: ["صح", "خطأ"], answer: 1 }, // خطأ، معايير نوعية
            
            // أسئلة إضافية منوعة لزيادة بنك الأسئلة
            { q: "مبدأ 'تقسيم العمل' عند فايول يؤدي بالضرورة إلى:", options: ["المركزية", "التخصص", "زيادة التكاليف", "بطء العمل"], answer: 1 },
            { q: "نظرية Y المتفائلة تفترض أن الموظف يبحث عن المسؤولية ويهتم بالتقدير المعنوي.", options: ["صح", "خطأ"], answer: 0 },
            { q: "البيئة الخارجية العامة تؤثر بشكل مباشر وفوري ويومي على منشأة بعينها.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، تأثير غير مباشر
            { q: "إدارة الجودة الشاملة تركز على الوقاية من الأخطاء وليس فقط اكتشافها.", options: ["صح", "خطأ"], answer: 0 },
            { q: "الإجراءات هي خطوات زمنية متسلسلة لتنفيذ عمل ما.", options: ["صح", "خطأ"], answer: 0 },
            { q: "القرارات غير المبرمجة تحتاج وقتاً طويلاً واستشارة خبراء لغموض الموقف.", options: ["صح", "خطأ"], answer: 0 },
            { q: "المهارات الفنية هي القدرة على التواصل وحل النزاعات بين الأفراد.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، هذه المهارات الإنسانية
            { q: "تصنف المتغيرات السياسية والقانونية ضمن بيئة المنظمة:", options: ["الداخلية", "الخارجية الخاصة", "الخارجية العامة", "الصناعية"], answer: 2 },
            { q: "تعتبر الرقابة القضائية أداة لضبط سلوك الموارد البشرية وحماية الأموال.", options: ["صح", "خطأ"], answer: 0 },
            { q: "القرارات المبرمجة تعالج المواقف الاستثنائية والطارئة.", options: ["صح", "خطأ"], answer: 1 }, // خطأ
            { q: "أولى خطوات التخطيط العلمي هي:", options: ["وضع الافتراضات", "تحديد الأهداف", "تقييم البدائل", "جمع المعلومات"], answer: 1 },
            { q: "أولى خطوات اتخاذ القرار هي:", options: ["طرح البدائل", "اختيار البديل الأمثل", "تحديد المشكلة بدقة", "المتابعة"], answer: 2 },
            { q: "وضع المعايير هو الخطوة الأولى في العملية الرقابية.", options: ["صح", "خطأ"], answer: 0 },
            { q: "الرقابة المتزامنة تتم أثناء سير العمل وممارسة النشاط.", options: ["صح", "خطأ"], answer: 0 },
            { q: "ضيق الوقت يعتبر من المعوقات التنظيمية في الاتصال.", options: ["صح", "خطأ"], answer: 1 }, // خطأ، بل معوق زمني مباشر مستقل
            { q: "نطاق الإشراف الضيق يؤدي إلى زيادة عدد المستويات الإدارية بالمنظمة.", options: ["صح", "خطأ"], answer: 0 }
        ];

        let selectedQuestions = []; // مصفوفة الأسئلة المختارة للاختبار الحالي
        const EXAM_LENGTH = 20; // عدد الأسئلة في الاختبار الواحد

        // دالة خلط المصفوفات (Randomize)
        function shuffleArray(array) {
            let currentIndex = array.length, randomIndex;
            while (currentIndex !== 0) {
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;
                [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
            }
            return array;
        }

        // تهيئة وبناء الاختبار
        function initExam() {
            const container = document.getElementById('quiz-container');
            container.innerHTML = ''; // مسح الحاوية
            
            // سحب 20 سؤال عشوائي من بنك الأسئلة
            let shuffledBank = shuffleArray([...fullQuestionBank]);
            selectedQuestions = shuffledBank.slice(0, EXAM_LENGTH);

            // بناء واجهة الأسئلة
            selectedQuestions.forEach((qObj, index) => {
                const card = document.createElement('div');
                card.className = 'question-card';
                card.id = `q-card-${index}`;

                const qText = document.createElement('p');
                qText.className = 'q-text';
                qText.innerHTML = `${index + 1}. ${qObj.q}`;
                card.appendChild(qText);

                const grid = document.createElement('div');
                grid.className = 'options-grid';

                qObj.options.forEach((optText, optIndex) => {
                    const btn = document.createElement('button');
                    btn.className = 'option-btn';
                    btn.innerHTML = optText;
                    btn.onclick = () => selectOption(index, optIndex, btn);
                    grid.appendChild(btn);
                });

                card.appendChild(grid);
                container.appendChild(card);
            });
        }

        // اختيار إجابة
        function selectOption(qIndex, optIndex, btnElement) {
            const card = document.getElementById(`q-card-${qIndex}`);
            // منع تغيير الإجابة إذا تم تسليم الاختبار
            if(card.classList.contains('submitted')) return; 

            const buttons = card.querySelectorAll('.option-btn');
            buttons.forEach(b => b.classList.remove('selected'));
            btnElement.classList.add('selected');
            
            // حفظ إجابة المستخدم في الكارت
            card.setAttribute('data-user-ans', optIndex);
        }

        // حساب الدرجة والتصحيح
        function evaluateExam() {
            let score = 0;
            let answeredCount = 0;

            selectedQuestions.forEach((qObj, index) => {
                const card = document.getElementById(`q-card-${index}`);
                card.classList.add('submitted'); // قفل الكارت
                
                const buttons = card.querySelectorAll('.option-btn');
                buttons.forEach(b => b.disabled = true); // تعطيل الأزرار
                
                const userAns = card.getAttribute('data-user-ans');

                if (userAns !== null) {
                    answeredCount++;
                    const userAnsInt = parseInt(userAns);
                    
                    if (userAnsInt === qObj.answer) {
                        score++;
                        buttons[userAnsInt].classList.add('correct-ans');
                    } else {
                        buttons[userAnsInt].classList.add('wrong-ans');
                        buttons[qObj.answer].classList.add('correct-ans'); // توضيح الصحيح
                    }
                } else {
                    // إذا لم يجب على السؤال
                    buttons[qObj.answer].classList.add('correct-ans');
                }
            });

            // إظهار النتائج
            showResults(score);
            
            // إخفاء زر التسليم
            document.getElementById('submit-btn').style.display = 'none';
        }

        // عرض لوحة النتائج
        function showResults(score) {
            const percent = Math.round((score / EXAM_LENGTH) * 100);
            
            document.getElementById('score-percent').innerText = percent;
            document.getElementById('score-points').innerText = score;
            document.getElementById('total-points').innerText = EXAM_LENGTH;
            
            const levelDiv = document.getElementById('user-level');
            if (percent >= 90) {
                levelDiv.innerHTML = "المستوى: ممتاز مرتفع 🌟🏆";
                levelDiv.style.color = "#10b981";
            } else if (percent >= 80) {
                levelDiv.innerHTML = "المستوى: ممتاز ✨";
                levelDiv.style.color = "#059669";
            } else if (percent >= 70) {
                levelDiv.innerHTML = "المستوى: جيد جداً 👍";
                levelDiv.style.color = "#3b82f6";
            } else if (percent >= 50) {
                levelDiv.innerHTML = "المستوى: مقبول (تحتاج للتركيز) ⚠️";
                levelDiv.style.color = "#f59e0b";
            } else {
                levelDiv.innerHTML = "المستوى: ضعيف (راجع المادة جيداً) 📚❌";
                levelDiv.style.color = "#ef4444";
            }

            const board = document.getElementById('result-board');
            board.style.display = 'block';
            
            // تمرير الشاشة للأسفل لرؤية النتيجة
            board.scrollIntoView({behavior: 'smooth'});
        }

        // تشغيل الاختبار فور تحميل الصفحة
        window.onload = initExam;
    </script>
</body>
</html>
