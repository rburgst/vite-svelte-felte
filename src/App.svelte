<script lang="ts">
  import { createForm } from "felte";
  import { ValidationMessage, reporter } from "@felte/reporter-svelte";
  import { loop_guard } from "svelte/internal";

  type Item = {
    title: string;
  };
  type Data = {
    email: string;
    items: Item[];
  };
  type ItemValidation = {
    title: string[];
  };

  let submitted: Data | undefined;

  const { form, data, setData, errors, addField, unsetField } = createForm<Data>({
    initialValues: {
      email: "",
      items: [],
    },
    onSubmit(values) {
      submitted = values;
    },
    validate(values) {
      const errors: { email: string[]; items: ItemValidation[] } = {
        email: [],
        items: [],
      };
      if (!values.email) errors.email.push("Must not be empty");
      if (!/[a-zA-Z][^@]*@[a-zA-Z][^@.]*\.[a-z]{2,}/.test(values.email))
        errors.email.push("Must be a valid email");
      values.items.forEach((item, index) => {
        const itemError = { title: [] };

        if (!item.title) {
          itemError.title.push(`title ${index} must be a valid string`);
        } else if (item.title.length < 2) {
          itemError.title.push(`title ${index} must have length >= 2`);
        }
        errors.items.push(itemError);
      });
      console.log("done validation", errors)
      return errors;
    },
    extend: [reporter],
  });

  $: interests = $data.items;

  function removeInterest(index) {
    return () => unsetField(`items.${index}`);
  }

  function addInterest(index) {
    return () => addField(`items`, { title: "" }, index);
  }
</script>

<form use:form>
  <h1>Form</h1>
  <table>
    <tr>
      <td>
        <h5>Errors</h5>
        <pre>{JSON.stringify($errors, null, 2)}</pre>
        <h5>Data</h5>
        <pre>{JSON.stringify($data, null, 2)}</pre>
      </td>
      <td>
        <label for="email">Email</label>
        <input name="email" id="email" />
        <br />
        {#if interests.length === 0}
          <button type="button" on:click={addInterest(0)}>
            Add first Interest
          </button>
        {/if}
        <br />
        {#each interests as interest, index}
          <div>
            <input name="foo" on:change={(e) => {
              const newValue = e.currentTarget.value
              console.log(`setting item data ${index}`, newValue)
              setData(`items.${index}.title`, newValue);
            }}/>
            <button type="button" on:click={addInterest(index + 1)}>
              Add Interest
            </button>
            <button type="button" on:click={removeInterest(index)}>
              Remove Interest
            </button>
          </div>
        {/each}
      </td>
    </tr>
  </table>
</form>
