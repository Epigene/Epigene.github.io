Controller tests are slightly more complex than model tests bacause you have to operate within a request-response context.

The workflow is to call the request macro either in a setup `before` block or in the test itself.

```ruby
describe "GET show" do
  before :each do
    get(:show, { param1: "value1"}, format: :json)
  end

  it "should redirect to root if no user by email detected" do
    get(:show, { param2: "value2"}, format: :json)
  end

  # POST
  # post(:create, {param: "value"}, format: :html)

  # You can also use block notation to expect errors
  # expect{
  #   post(:create, {contact: Factory.attributes_for(:contact, :invalid)}
  # }.to raise_error(ErrorClass, /error_message_regex/)

  # Or to check changes
  # expect{
  #   post :create, contact: Factory.attributes_for(:contact)
  # }.to change(Contact, :count).by(1)
```

After calling the request macro you get a `response` object.  
You will be checking three things in the response context

### 1. Response status

```ruby
it "should return 200 status" do
  get :calendar, locale: "lv", email: "none@test.cz"
  expect(response.status).to eq 200
end
```

### 2. Response redirect

```ruby
it "should redirect to root path" do
  get :calendar, bad: "param"
  expect(response).to redirect_to(root_path)
end
```

### 3. Rendered template

```ruby
it "should render :calendar template" do
  get :calendar
  expect(response).to render_template(:calendar)
end
```

### 4. Response local variables (controller->view)

```ruby
it "should redirect to root path" do
  get :calendar, bad: "param"
  expect(assigns(:calendar).class).to eq(Calendar)
end
```

Consult [Rspec docs](https://www.relishapp.com/rspec/rspec-rails/docs/controller-specs) for more.

## Testing controller modules



Consult [Pivotal Labs' post](https://blog.pivotal.io/labs/labs/testing-modules-that-get-included-in-a-controller)
