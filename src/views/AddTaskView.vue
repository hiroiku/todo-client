<template>
  <section :class="$style.container">
    <h1 :class="$style.heading">タスクを追加する</h1>

    <form :class="$style.form" novalidate>
      <div :class="$style.field">
        <label for="title" :class="$style.label">タスク名</label>
        <input id="title" :class="$style.input" type="text" inputmode="text" placeholder="タスク名" />
      </div>

      <div :class="$style.field">
        <label for="description" :class="$style.label">説明</label>
        <textarea id="description" :class="$style.textarea" rows="4" placeholder="説明を入力"></textarea>
        <p :class="$style.help">任意。詳細やメモを記載</p>
      </div>

      <div :class="$style.fieldInline">
        <div :class="$style.field">
          <label for="priority" :class="$style.label">優先度</label>
          <select id="priority" :class="$style.select">
            <option value="low">低</option>
            <option value="medium">中</option>
            <option value="high">高</option>
          </select>
        </div>

        <div :class="$style.field">
          <label for="due" :class="$style.label">期限</label>
          <input id="due" :class="$style.input" type="date" placeholder="YYYY-MM-DD" />
        </div>
      </div>

      <div :class="$style.field">
        <label for="tags" :class="$style.label">タグ</label>
        <input id="tags" :class="$style.input" type="text" placeholder="例: work, urgent" />
        <p :class="$style.help">カンマ区切りで入力</p>
      </div>

      <div :class="$style.actions">
        <button :class="[$style.button, $style.primary]" type="button">追加</button>
        <button :class="[$style.button, $style.ghost]" type="button">クリア</button>
      </div>
    </form>
  </section>
</template>

<style lang="scss" module>
.container {
  max-width: 720px;
  margin: 0 auto;
  padding: 1.5rem 1rem;
}

.heading {
  margin: 0 0 1rem 0;
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--color-text);
  text-align: left;
}

.form {
  background-color: var(--color-background);
  border: 1px solid var(--color-border);
  border-radius: 0.75rem;
  padding: 1rem;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.fieldInline {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-bottom: 1rem;
}

.label {
  font-size: 0.875rem;
  color: var(--color-text);
}

.input,
.textarea,
.select {
  width: 100%;
  background-color: transparent;
  color: var(--color-text);
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;
  padding: 0.625rem 0.75rem;
}

.textarea {
  resize: vertical;
}

.input:focus,
.textarea:focus,
.select:focus {
  outline: 2px solid hsla(160, 100%, 37%, 0.35);
  border-color: hsla(160, 100%, 37%, 1);
}

.help {
  font-size: 0.75rem;
  color: var(--color-text);
  opacity: 0.7;
  margin: 0;
}

.error {
  font-size: 0.75rem;
  color: #c85151;
  margin: 0;
}

.actions {
  display: flex;
  gap: 0.75rem;
  margin-top: 0.5rem;
}

.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.6rem 1rem;
  border-radius: 0.5rem;
  border: 0;
  cursor: pointer;
  font-weight: 600;
}

.primary {
  background-color: hsla(160, 100%, 37%, 1);
  color: var(--color-background);
}

.primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.ghost {
  background-color: transparent;
  color: var(--color-text);
  border: 1px solid var(--color-border);
}
</style>
