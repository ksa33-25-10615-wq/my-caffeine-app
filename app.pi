import streamlit as st
import numpy as np
import matplotlib.pyplot as plt

# 한글 폰트 설정 (웹 서버 배포용 나눔고딕 적용)
plt.rc('font', family='NanumGothic', size=9)
plt.rcParams['axes.unicode_minus'] = False

st.set_page_config(page_title="개인 맞춤형 카페인 대사 관리 시스템", layout="centered")

# ==========================================
# CSS 스타일링: Spoqa 폰트 및 아이콘 겹침 완벽 해결
# ==========================================
st.markdown("""
<style>
@import url('https://spoqa.github.io/spoqa-han-sans/css/SpoqaHanSansNeo.css');

/* 글씨가 들어가는 영역에만 예쁜 폰트 적용 */
html, body, [class*="st-"], p, h1, h2, h3, h4, h5, h6, label {
    font-family: 'Spoqa Han Sans Neo', 'Spoqa Han Sans', sans-serif !important;
}

/* 화살표 아이콘 글자 겹침 방지 (원래 아이콘 폰트로 보호) */
span[data-testid*="Icon"], .material-symbols-rounded, .material-icons {
    font-family: 'Material Symbols Rounded', 'Material Icons' !important;
}

/* 제목 및 헤더 굵기/크기 축소 */
h1 { font-size: 1.45rem !important; font-weight: 500 !important; padding-bottom: 0.3rem; }
h2 { font-size: 1.2rem !important; font-weight: 500 !important; }
h3 { font-size: 1.05rem !important; font-weight: 500 !important; }

/* 라벨 및 선택지 텍스트 정돈 */
.stRadio label, .stCheckbox label, .stSelectbox label, .stSlider label {
    font-size: 0.9rem !important;
    font-weight: 400 !important;
    white-space: nowrap !important;
}

/* 수치 측정 항목 크기 조정 */
[data-testid="stMetricValue"] { font-size: 1.2rem !important; font-weight: 500 !important; }
[data-testid="stMetricLabel"] { font-size: 0.8rem !important; font-weight: 400 !important; }
hr { margin: 1rem 0 !important; }
</style>
""", unsafe_allow_html=True)

if 'page' not in st.session_state:
    st.session_state.page = 'survey'
if 'drink_list' not in st.session_state:
    st.session_state.drink_list = []

# ==========================================
# [화면 1] 초기 설문조사
# ==========================================
if st.session_state.page == 'survey':
    st.title("개인 맞춤형 카페인 민감도 및 대사 진단")
    st.write("간 효소(CYP1A2) 활성도 및 유전자형 추정을 위한 설문 항목입니다.")
    st.markdown("---")

    st.subheader("1. 카페인 민감도 설문")
    
    q1 = st.radio("1. 평소 카페인 음료 섭취 빈도", ["거의 마시지 않음", "주 1~2회", "주 3~5회", "거의 매일"], horizontal=True)
    q2 = st.radio("2. 커피 섭취 후 각성 효과 지속 시간", ["1~2시간", "3~4시간", "5~6시간", "7시간 이상"], horizontal=True)
    q3 = st.radio("3. 오후 섭취 시 입면 장애(불면) 여부", ["전혀 아니다", "가끔 그렇다", "자주 그렇다", "거의 항상 그렇다"], horizontal=True)
    q4 = st.radio("4. 섭취 후 심계항진(두근거림) 경험", ["없다", "가끔 있다", "자주 있다"], horizontal=True)
    q5 = st.radio("5. 평소 취침 시각", ["오후 10시 이전", "오후 10~11시", "오후 11시~자정", "자정 이후"], horizontal=True)
    q6 = st.radio("6. 시험기간 일일 카페인 섭취량", ["0잔", "1잔", "2잔", "3잔 이상"], horizontal=True)
    q7 = st.radio("7. 주로 섭취하는 음료 종류", ["아메리카노", "라떼", "에너지음료", "차류", "기타"], horizontal=True)

    st.markdown("---")
    st.subheader("2. 생체 조건 및 대사 변수 (CYP1A2 효소 활성 요인)")

    col1, col2 = st.columns(2)
    with col1:
        gender = st.radio("성별", ["남성", "여성"], horizontal=True)
        weight = st.number_input("체중 (kg)", min_value=30.0, max_value=120.0, value=60.0, step=1.0)
        is_smoker = st.checkbox("흡연 여부 (PAH 성분의 효소 유도)")

    with col2:
        takes_pill = False
        if gender == "여성":
            takes_pill = st.checkbox("경구 피임약 복용 (CYP1A2 억제)")
        has_grapefruit = st.checkbox("오늘 자몽주스 섭취 (나린진 성분 저해)")
        ate_cruciferous = st.checkbox("오늘 십자화과 채소/직화구이 섭취")

    st.markdown("---")
    
    if st.button("대사 타입 진단 및 분석 시작", use_container_width=True):
        score = 0
        score += ["1~2시간", "3~4시간", "5~6시간", "7시간 이상"].index(q2)
        score += ["전혀 아니다", "가끔 그렇다", "자주 그렇다", "거의 항상 그렇다"].index(q3)
        score += ["없다", "가끔 있다", "자주 있다"].index(q4)

        if score <= 2:
            gene_type = "AA형 (빠른 대사형)"
            base_t_half = 4.0
        elif score <= 5:
            gene_type = "AC형 (중간 대사형)"
            base_t_half = 5.5
        else:
            gene_type = "CC형 (느린 대사형)"
            base_t_half = 8.0

        multiplier = 1.0
        if is_smoker:
            multiplier *= 0.6
        if ate_cruciferous:
            multiplier *= 0.85
        if takes_pill:
            multiplier *= 1.5
        if has_grapefruit:
            multiplier *= 1.3

        final_t_half = base_t_half * multiplier

        sleep_time_map = {
            "오후 10시 이전": 21,
            "오후 10~11시": 22,
            "오후 11시~자정": 23,
            "자정 이후": 24
        }
        
        st.session_state.gene_type = gene_type
        st.session_state.t_half = final_t_half
        st.session_state.weight = weight
        st.session_state.gender = gender
        st.session_state.sleep_target = sleep_time_map[q5]
        st.session_state.page = 'dashboard'
        st.rerun()

# ==========================================
# [화면 2] 다중 음료 입력 & 누적 대사 분석
# ==========================================
elif st.session_state.page == 'dashboard':
    st.title("실시간 체내 카페인 누적 잔류량 및 수면 분석")
    
    st.info(f"진단 타입: {st.session_state.gene_type} | 산출 반감기(T_1/2): {st.session_state.t_half:.1f}시간")

    st.markdown("---")
    st.subheader("오늘 마신 음료 추가하기 (다중 등록 가능)")

    col_a, col_b, col_c = st.columns([2, 2, 1])
    
    with col_a:
        category = st.selectbox("음료 종류", ["아메리카노 (150mg)", "라떼 (122mg)", "콜드브루 (212mg)", "몬스터 (100mg)", "핫식스 (60mg)", "캔커피 (70mg)", "녹차/홍차 (35mg)", "직접 입력"])
        
        if category == "직접 입력":
            caffeine_input = st.number_input("카페인 함량(mg)", min_value=0, value=100)
            drink_name = "직접 입력 음료"
        else:
            drink_map = {
                "아메리카노 (150mg)": 150, "라떼 (122mg)": 122, "콜드브루 (212mg)": 212,
                "몬스터 (100mg)": 100, "핫식스 (60mg)": 60, "캔커피 (70mg)": 70, "녹차/홍차 (35mg)": 35
            }
            caffeine_input = drink_map[category]
            drink_name = category.split(" ")[0]

    with col_b:
        drink_time_input = st.slider("섭취 시각 (시)", 0, 23, 9)

    with col_c:
        st.write(" ")
        st.write(" ")
        if st.button("음료 추가"):
            st.session_state.drink_list.append({
                "name": drink_name,
                "caffeine": caffeine_input,
                "time": drink_time_input
            })
            st.rerun()

    if st.session_state.drink_list:
        st.markdown("**현재 등록된 섭취 리스트:**")
        cols = st.columns([3, 1])
        with cols[0]:
            items_str = ", ".join([f"{d['time']}시: {d['name']}({d['caffeine']}mg)" for d in st.session_state.drink_list])
            st.text(items_str)
        with cols[1]:
            if st.button("목록 초기화"):
                st.session_state.drink_list = []
                st.rerun()
    else:
        st.caption("아직 추가된 음료가 없습니다. 위에서 음료를 선택 후 '음료 추가'를 눌러주세요.")

    st.markdown("---")
    
    sleep_target = st.slider("목표 취침 시각 설정 (시)", 0, 24, st.session_state.sleep_target)

    t_half = st.session_state.t_half
    k = np.log(2) / t_half
    hours = np.linspace(0, 24, 500)
    c_t = np.zeros_like(hours)

    total_caffeine_ingested = sum(d["caffeine"] for d in st.session_state.drink_list)

    for drink in st.session_state.drink_list:
        t_d = drink["time"]
        mg = drink["caffeine"]
        c_t += np.where(hours >= t_d, mg * np.exp(-k * (hours - t_d)), 0)

    idx_sleep = np.abs(hours - sleep_target).argmin()
    c_at_sleep = c_t[idx_sleep]

    m1, m2, m3 = st.columns(3)
    m1.metric("총 섭취 카페인량", f"{total_caffeine_ingested} mg")
    m2.metric("취침 시 예상 잔류량", f"{c_at_sleep:.1f} mg")
    m3.metric("체중 반영 분포용적(Vd)", f"{st.session_state.weight * 0.6:.1f} L")

    if c_at_sleep <= 50:
        st.success("[안전] 잔류량이 50mg 이하로 아데노신 결합에 미치는 영향이 적어 수면에 지장이 없습니다.")
    elif c_at_sleep <= 150:
        st.warning("[주의] 잔류 카페인으로 인해 아데노신 결합이 억제되어 수면 구조 방해 및 두근거림이 생길 수 있습니다.")
    else:
        st.error("[위험] 잔류 카페인 과다 상태입니다. 불면증, 교감신경 과활성화 및 심혈관 부담 위험이 높습니다.")

    fig, ax = plt.subplots(figsize=(8, 3.2))
    ax.plot(hours, c_t, color="#2c3e50", linewidth=1.8, label="체내 누적 카페인(mg)")
    ax.axhline(50, color="#27ae60", linestyle="--", linewidth=1, label="수면 안전 기준선 (50mg)")
    ax.axvline(sleep_target, color="#c0392b", linestyle=":", linewidth=1, label=f"목표 취침 시각 ({sleep_target}시)")
    ax.fill_between(hours, c_t, color="#bdc3c7", alpha=0.25)
    ax.set_xlabel("시간 (0시 ~ 24시)")
    ax.set_ylabel("카페인 잔류량 (mg)")
    ax.set_title("1차 반응속도론 및 중첩 원리 기반 체내 카페인 대사 곡선", fontsize=10)
    ax.legend(loc="upper right", fontsize=8)
    ax.grid(True, alpha=0.2)
    st.pyplot(fig)

    st.markdown("---")

    with st.expander("수면 및 대사 촉진을 위한 생화학적 가이드라인", expanded=False):
        col_a, col_b = st.columns(2)
        with col_a:
            st.markdown("**1. 체내 배출 및 대사 촉진**")
            st.write("- 미온수 500ml 이상 섭취: 카페인 이뇨 작용 보충 및 신장 배출 유도")
            st.write("- 십자화과 채소 섭취: 브로콜리/양배추의 I3C 성분이 CYP1A2 효소 생산 유도")
            st.write("- 가벼운 유산소 운동: 간 혈류량을 늘려 대사 회전율 향상")

        with col_b:
            st.markdown("**2. 신경계 완화 및 저해 물질 차단**")
            st.write("- L-테아닌 / 캐모마일: GABA 증진을 통한 신경 과활성화 및 두근거림 완화")
            st.write("- 바나나 / 마그네슘: 신경 및 근육 이완을 통한 교감신경 가라앉힘")
            st.write("- 자몽주스/피임약 지양: CYP1A2 활성 자리를 점유하여 배출을 지연시키므로 주의")

    st.markdown("---")
    if st.button("설문지 다시 작성하기"):
        st.session_state.page = 'survey'
        st.session_state.drink_list = []
        st.rerun()